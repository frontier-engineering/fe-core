---
title: How a Harbor trial works in Wharf
description: Task configuration, image preparation, agent execution, patch transfer, grading, and baseline checks
---

# How a Harbor trial works in Wharf

Before tuning concurrency, understand the work being scaled. A **task** is
the benchmark problem, environment definition, and grading contract. A
**trial** is one attempt at that task. A **job** contains the selected tasks
and attempts; `K=4` requests four logical attempts per selected task, not
four stages of one attempt.

Harbor defines the task format; Wharf is a runner for a supported subset of
that format. This page describes Wharf's Copilot and separate-Docker-verifier
path at the knowledge pack's pinned revision, not every Harbor execution
mode. Harbor can also verify in a shared environment.

## 1. Read the task contract

Start with `task.toml`, then inspect the files it accompanies:

| Component | Purpose |
|---|---|
| `task.toml` | Task identity and metadata, image selection, agent/setup/verifier timeouts, resource and environment declarations, and artifact paths |
| `instruction.md` | The problem given to the agent; singular `instruction`, not `instructions` |
| `environment/Dockerfile` | Agent environment build recipe when no `environment.docker_image` is configured |
| `tests/Dockerfile` | Dedicated verifier environment and bundled grading files for the Docker path described here |
| `tests/test.sh` | Runs the task's grading procedure and writes reward evidence |
| `solution/solve.sh` | Optional reference solution for Harbor's oracle baseline; not the learner agent's instructions |

Use the retained
[image-backed task manifest](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/tests/fixtures/harbor/abs-module-cache-flags/task.toml)
to identify the task ID, repository base commit, agent and verifier timeouts,
image reference, and patch artifact declaration. Compare it with the
[Dockerfile-backed smoke manifest](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/enterprise/smoke-tasks/smoke-dotnet/task.toml),
which has no `docker_image`.

The manifest is input, not proof that a runner enforces every declaration.
Wharf's loader accepts schema `1.3` and extracts a specific set of fields;
run-level timeout overrides take precedence over task timeouts. Do not infer
support for every current Harbor option, network policy, or resource limit
from its presence in a task file. Trace it through the runner when it matters.

**Evidence:** [Task loader](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/src/wharf/Task/HarborTaskLoader.cs)
and [run orchestration and timeout precedence](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/src/wharf/Run/RunService.cs).

## 2. Prepare images, then create an isolated attempt

Agent preparation follows this chain:

```text
environment.docker_image configured?
  yes -> inspect locally; pull if absent; use it as the task environment
  no  -> prepare the base and build environment/Dockerfile
      -> obtain the cached exact-version Copilot CLI payload
      -> obtain the cached task-environment + CLI composition
      -> build a unique trial child image
      -> create/start the trial container and prepare its workspace
```

In Wharf, a configured `environment.docker_image` is authoritative: the
agent path does not also rebuild `environment/Dockerfile`. The two are
alternative paths, not mandatory consecutive steps.

There are several kinds of reuse, not one undifferentiated "Docker cache":

| Reusable object | What it avoids | What still happens |
|---|---|---|
| Pulled task image and shared Docker layers | Downloading already-present image content | Image checks and trial preparation |
| Persistent Dockerfile-backed task environment | Rebuilding an unchanged task environment | CLI composition checks and trial preparation |
| Versioned Copilot CLI payload image | Repeating the CLI npm installation for every attempt | Matching the effective version and platform |
| Composed task-plus-CLI image | Reassembling the same environment and CLI payload | Creating a unique trial child and fresh container |
| Verifier Docker build layers | Repeating unchanged build steps where Docker can reuse layers | A separately tagged verifier build and fresh verifier container |

The CLI payload identity includes the exact effective version, platform, and
recipe. The composed agent cache uses environment and CLI image identities,
not just mutable tags. Workspace preparation validates the cached CLI version.
An image is a reusable template; a container is an individual execution.
Reusing images must not mean reusing a previous agent's modified workspace.

Cold preparation can download, unpack, and write substantial image content.
The retained ABS and Adaptix Dockerfiles both name the same Mars base
([ABS](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/tests/fixtures/harbor/abs-module-cache-flags/environment/Dockerfile),
[Adaptix](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/tests/fixtures/harbor/adaptix-name-mapping-aliases/environment/Dockerfile)).
Shared layers reduce duplicate transfers, but do not make all task-specific
layers free or eliminate Docker storage pressure. The operator reports this
as significant disk activity across DeepSWE images; the fixture recipes do
not measure the entire registry's layer reuse or disk cost.

Caching the CLI removes repeated installation work, not the first population
cost. The operator's estimate is roughly 20 seconds saved per attempt. The
retained cache research measured a direct install at **26.51 seconds**, a
first task-derived CLI image build at **66.63 seconds**, and a retained-image
inspection at **0.21 seconds** on that host and CLI version. These are
different operations, not a universal per-trial speedup or a benchmark of
the final cache pipeline.

**Evidence:** [Image preparation implementation](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/src/wharf/Image/DockerAgentImagePreparer.cs),
[cache research and measurements](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/.github/tracking/2026-10-10-shared-copilot-cli-cache-research.md),
and [cache review](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/.github/tracking/2026-10-10-shared-copilot-cli-cache-review.md).

## 3. Run the agent and retain the patch

Wharf supplies `instruction.md` as the agent prompt. The agent edits the
repository in its isolated container. At the end, Wharf captures a Git diff
against the starting commit, including binary changes and eligible new
untracked files. The handoff is that retained patch, not a shared live
filesystem and not merely the agent's claim that it finished.

Within the job's trial evidence, the patch is retained as `agent/patch.diff`
and copied to `artifacts/model.patch`. Agent logs, trajectory, result, and
available usage/cost evidence are retained too. The agent container is
removed after capture; the agent does not keep working during grading.
Successful agent execution is not yet a passing task result.

**Evidence:** [Agent execution, patch capture, and container cleanup](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/src/wharf/Agent/AgentExecution.cs)
and [artifact handoff](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/src/wharf/Run/RunService.cs).

## 4. Grade the patch in a separate environment

For these Docker-backed tasks, Wharf builds `tests/Dockerfile` under a unique
verifier tag, allowing Docker to reuse available base images and build
layers. This is not a promise to pull the agent's composed CLI image or to
reuse a persistent Wharf verifier cache: verifier-image caching was outside
the shared CLI-cache change.

The verifier image supplies a pristine repository and grading dependencies.
The task's Dockerfile bundles `test.sh`, the grader, configuration, and test
patch into that image. Wharf creates a fresh verifier container and transfers
the retained artifacts to `/logs/artifacts/`; it does not transfer the
agent's entire modified workspace.

In the DeepSWE-style grader used by the smoke example:

1. `/tests/test.sh` invokes the grader's preparation step.
2. The grader replays `model.patch` against the intended base state.
3. It applies the evaluator's `test.patch`, separate from the model's patch.
4. The test script runs the suites and invokes grading.
5. The grader writes reward and test evidence under `/logs/verifier/`.
   Wharf copies that evidence into the job's trial directory and records
   the trial result before removing the verifier container.

This distinction matters: tests may be bundled during image construction
rather than copied as loose files after patch replay. For this Docker path,
actual model/test patch application occurs inside the grader, not in the
runner's host-side pristine-workspace preparation.

Fail-to-pass (**F2P**) checks show the requested behavior was fixed;
pass-to-pass (**P2P**) checks catch regressions. The example's binary reward
requires nonempty F2P evidence, all F2P checks passing, and no P2P failures.
A shell exit code alone is not sufficient evidence of a solved task.

If verification times out, retry only verification with the exact retained
patch and original agent cost, in a fresh environment. Do not rerun the agent
to obtain a different patch. Ordinary reward-zero results are not retried.

**Evidence:** [Verifier build and orchestration](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/src/wharf/Run/RunService.cs),
[verifier lifecycle and evidence](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/src/wharf/Verifier/VerifierExecution.cs),
and the smoke task's
[verifier Dockerfile](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/enterprise/smoke-tasks/smoke-dotnet/tests/Dockerfile),
[test script](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/enterprise/smoke-tasks/smoke-dotnet/tests/test.sh),
and [grader contract](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/enterprise/smoke-tasks/smoke-dotnet/tests/grader.py).

## 5. Locate the preparation gate

`N` limits trial concurrency. `P` limits concurrent preparation operations
across agent and verifier work on the same host. It is **one shared limit**,
not `P` agent preparations plus another `P` verifier preparations.

| Phase | Uses the shared `P` gate? |
|---|---|
| Agent image inspection, pull/build, cache composition, and workspace preparation | Yes |
| Agent infrastructure-retry workspace preparation | Yes |
| Agent reasoning and editing | No |
| Verifier image build and runner-side pristine-workspace/patch preparation | Yes |
| Verifier container execution, including the task grader's patch replay and tests | No |

The permit is released between preparation and execution. A cached trial
still passes through preparation; the cache makes that work cheaper rather
than bypassing isolation or the gate. `P` counts active preparation
operations, not retained images, and does not limit the entire grading
duration. In the Docker path above, the runner retains the patch for the
grader instead of applying it on the host.

Consequently, a cold run can saturate Docker storage even when the CPU can
support more agents. A warm run does not qualify the cold settings. Size `P`
from cold-case evidence, preserve gate telemetry and cache identities, and
keep `N` separate when testing a change.

**Evidence:** [Shared gate implementation](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/src/wharf/Run/PreparationGate.cs),
[gate boundaries in the runner](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/src/wharf/Run/RunService.cs),
and [preparation-gate research](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/.github/tracking/2026-10-08-shared-preparation-gate-research.md).

## 6. Understand the no-op and oracle baselines

Harbor provides `nop` (no-op) and `oracle` agents for checking the evaluation
itself:

| Baseline | Work submitted | What it checks |
|---|---|---|
| `nop` | No solution changes | The unfixed base should not earn a solved-task reward; existing P2P checks should still pass |
| `oracle` | The task's reference solution, normally `solution/solve.sh` | A known solution can satisfy the evaluator in the intended environment |

Together they help distinguish a useful task from one that already passes,
has broken setup, or cannot recognize its reference solution. They are task
validation controls, not measurements of an LLM's ability. Oracle needs a
reference solution; an omitted solution is not proof of an unsolvable task.

These are not implemented as selectable baseline execution modes in the
Wharf runner at the pinned revision. Its normal path prepares Copilot, and
a clean no-patch agent exit is terminal `AgentUnfinished`, not an intentional
`nop` trial sent to grading. The grader's ability to handle an absent patch
does not establish a Wharf no-op mode. Do not invent a Wharf baseline flag
or claim baseline coverage from a regular no-patch result.

**Sources:** Harbor's current [task overview](https://docs.harborframework.com/tasks/overview),
[separate verifier](https://docs.harborframework.com/tasks/separate-verifier),
[agent list](https://docs.harborframework.com/agents/pre-integrated-agents),
and [reference solution documentation](https://docs.harborframework.com/tasks/solution),
consulted 2026-10-29. These upstream pages can change; the Wharf implementation
claims above remain tied to commit `fd9c194`. The smoke grader documents its
F2P/P2P whitelists as derived from the oracle-versus-nop differential; that is
dataset provenance, not evidence that Wharf exposes those baseline modes.

## Explain it back before scaling

Without following the headings, sketch the path from manifest to reward.
Mark reusable images, fresh containers, the patch crossing the isolation
boundary, and both acquisitions/releases of the shared preparation gate.
Then explain what changes on a warm run and what must remain identical on a
verifier retry. Use the [study guide](study-guide.md#module-0-the-harbor-trial-being-scaled)
and learner assessments to check your model before running larger jobs.
