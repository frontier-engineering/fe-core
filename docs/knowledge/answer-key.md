---
title: Assessment answer key
description: Assessor-only answers, rationale, evidence, and scoring rubric for the Wharf assessments
---

## Assessor use only

Do not distribute this file with the assessments.

## Assessment 1: Foundations

Score one point per multiple-choice answer and up to two points per short
answer: one for the correct principle and one for the reasoning or evidence.
There are six multiple-choice and ten short-answer questions. The maximum
is 26; 80% is 20.8, so 21 points passes.

| # | Answer | Rationale | Evidence |
|---|---|---|---|
| 1 | C | PASS@1 uses every retained logical trial, including failed, timed-out, and cancelled ones. | [Lesson 9](lessons.md#9-missing-is-not-zero-and-the-denominator-is-planned) |
| 2 | B | Verifier failures stay inside the verifier-only retry boundary. | [Lesson 3](lessons.md#3-retries-must-never-change-what-you-are-measuring) |
| 3 | C | A clean exit with no patch is terminal `AgentUnfinished`. | [Lesson 3](lessons.md#3-retries-must-never-change-what-you-are-measuring) |
| 4 | C | The cold case is the worst case; higher warm `P` saves about a minute. | [Lesson 1](lessons.md#1-scale-up-before-you-scale-out) |
| 5 | B | Missing costs are never imputed, including from batch bills. | [Lesson 9](lessons.md#9-missing-is-not-zero-and-the-denominator-is-planned) |
| 6 | C | The 1.1.0 package passed metadata checks but was missing its DLL. | [Lesson 7](lessons.md#7-test-the-deliverable-not-its-description) |

**7.** `N` limits concurrent trials and is mostly bounded by CPU and memory
while agents run. `P` limits preparation work: image pulls and builds, CLI
payload setup, workspace creation, and patch application. It protects the
Docker daemon, storage driver, and disk from I/O bursts. Full credit requires
both resources and the reason they are independent.

**8.** Other waiters depend on the same build. If one caller's cancellation
ended the shared work, every duplicate request would fail. Cancellation
belongs at the wait boundary, and failed builds are removed from the
in-flight map so they can be retried.

**9.** A slow telemetry sink would otherwise keep the slot looking occupied,
blocking new work and undermining work-conserving scheduling.

**10.** Those checks could reward implementations that echoed prompt details
without proving behavior. Executable contract tests verify behavior.

**11.** The suites differ in tasks, languages, size, cost, and difficulty. They
are separate datasets with separate manifests, so their pass rates are not
interchangeable.

**12.** Record run identity and configuration (versions, model, `K`, `N`, `P`),
host and cache state, results, exact errors, telemetry, and diagnosis, in
`.github/tracking/`. The failed run is often the only evidence of why a limit
or fix exists.

**13.** The manifest supplies task metadata and execution declarations;
`instruction.md` is the agent's problem; `tests/test.sh` performs grading
and produces reward evidence. A configured `environment.docker_image`
takes precedence in Wharf's agent path, bypassing the agent Dockerfile.
Award one point for distinguishing the three files and one for the image
precedence. See [the task contract](harbor-trial.md#1-read-the-task-contract).

**14.** The task image supplies the environment; the exact-version/platform
CLI payload avoids repeating npm installation; the composed cache combines
their identities. Each attempt still gets a unique trial child image and
fresh container/workspace. Award one point for the cache/isolation distinction
and one for recognizing that cold population, cache hits, and host conditions
have different costs: roughly 20 seconds is an operator estimate, not a
universal speedup. The retained direct-install measurement was 26.51 seconds.
See [image preparation](harbor-trial.md#2-prepare-images-then-create-an-isolated-attempt).

**15.** Wharf captures the Git diff as `agent/patch.diff` and copies it to
`artifacts/model.patch`, removes the agent container, and transfers the
artifact to a fresh verifier. The Dockerfile bundles evaluator files; the
smoke task's grader applies the model patch and evaluator test patch inside
the verifier when `/tests/test.sh` runs. Reward, test reports, command logs,
and trial-result evidence establish whether F2P and P2P checks passed; the
agent's exit code establishes no such result. Award one point for the
retained-patch/fresh-verifier handoff and one for the grader/test/evidence
distinction. See [agent capture](harbor-trial.md#3-run-the-agent-and-retain-the-patch)
and [separate grading](harbor-trial.md#4-grade-the-patch-in-a-separate-environment).

**16.** `nop` checks the unfixed base, which should not earn solved-task
reward; `oracle` checks that a reference solution can pass the evaluator.
They validate the task, not LLM ability. Neither is a selectable baseline
execution mode in the pinned Wharf runner; a normal clean no-patch agent
exit is terminal `AgentUnfinished`, not a successful no-op baseline.
Award one point for the baseline purposes and one for the Wharf distinction.
See [baseline controls](harbor-trial.md#6-understand-the-no-op-and-oracle-baselines).

## Assessment 2: Scenarios

Score each scenario from 0 to 4 using the rubric, for a maximum of 16. A
passing result is 12 or higher, with no scenario scored 0.

| Score | Meaning |
|---|---|
| 4 | Correct decision, clear reasoning, and specific evidence to cite or collect |
| 3 | Correct decision and reasoning; evidence is general |
| 2 | Partially correct decision, or correct decision without reasoning |
| 1 | Identifies the issue but recommends an unsafe or unsupported action |
| 0 | Incorrect or missing |

### Scenario 1: expected elements

* The likely cause is preparation I/O overwhelming Docker storage on a cold
  run, because `P` was too high, not because `N` was.
* Recommend reducing `P` to a conservative value sized for the cold case and
  keeping it for every run. A reasonable starting point is near the z01
  ratio, about `P=12` per 16 cores scaled with storage capacity, while `N`
  can stay near 2× CPU. The enterprise suite has only 56 trials, so `N` above
  56 adds nothing.
* Record a tracking entry with configuration, host, cold-cache state, elapsed
  time, errors, and Docker logs before deletion.
* Show preparation-gate telemetry: queue waits are measured in seconds, while
  the failure cost a whole run.

### Scenario 2: expected elements

* Two single runs cannot establish a causal regression. Model behavior and
  service conditions can change between runs. P2P-error trials are worth
  investigating.
* Collect repeated runs of both versions under controlled conditions, check
  the specific P2P failures, and confirm the evaluator revision is the same.
* Describe it as an observed difference that warrants investigation, not a
  confirmed regression.

### Scenario 3: expected elements

* Not proven. Accepting and rendering a setting does not prove execution
  consumes it, as the 1.0.0 review found.
* Add a test that runs execution with the override and asserts the effective
  timeout at the agent boundary, including precedence over task values.
* Execute the packaged artifact, and check filesystem and Docker residue after
  failure paths. An independent code review also counts.

### Scenario 4: expected elements

* Use the configured `docker_image`, not an additional environment Dockerfile
  build. Reuse valid task/CLI/composed image caches, but create a unique
  trial child image and fresh container/workspace. Capture the patch, remove
  the agent container, and transfer the patch to a fresh verifier. The
  verifier Dockerfile bundles grading files; its grader applies the model
  and evaluator patches at runtime. Do not grade a reused live agent workspace.
* `P=2` permits at most two preparation operations total, not two of each
  kind. Both warm agent preparation and verifier preparation use the shared
  gate. Agent preparation releases it before agent execution; verifier
  image/runner-workspace preparation releases it before verifier execution.
  The Docker grader's model/test patch replay is inside execution, outside
  that permit. The cache reduces work, not the isolation or gate contract.
* Retry only the verifier with the exact retained patch and unchanged agent
  cost/identity, creating a fresh verifier environment. Preserve the failed
  verifier evidence and compare retained patch content or hashes and trial/
  retry identifiers. Image/cache and grading-file identities make the replay
  auditable; a new agent attempt would be a different answer.
* Explain `nop` as the unfixed-base control and `oracle` as the known-solution
  control. Do not claim Wharf runs either as a selectable baseline mode.
  A normal no-patch exit is terminal unfinished, not a passing baseline.

**Evidence:** [Harbor trial primer](harbor-trial.md), especially its pinned
runner, image preparation, agent capture, and smoke-grader sources.
For full credit, address all four areas with specific evidence; a response
that endorses sharing a modified agent workspace or rerunning the agent for
the verifier timeout cannot receive full credit.
