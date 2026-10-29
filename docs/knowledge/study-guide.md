---
title: Wharf study guide
description: Eight modules for learning Harbor trial mechanics and the engineering judgment recorded in Wharf's evidence
---

## How to use this guide

Work through the modules in order. For each, read the listed evidence, then
answer the self-check questions in your own words before checking the source.
Read [the Harbor trial primer](harbor-trial.md) and [lessons.md](lessons.md)
first for an overview.

Estimated time: four to six hours of reading, plus the optional failure labs.

## Module 0: The Harbor trial being scaled

**Goal:** Trace one attempt from task configuration through agent execution
to a separately verified result, before reasoning about concurrency.

**Read:**

* [How a Harbor trial works in Wharf](harbor-trial.md), including its pinned
  manifest, image-cache, runner, and smoke-grader evidence
* Harbor's [task overview](https://docs.harborframework.com/tasks/overview)
  and [reference solution](https://docs.harborframework.com/tasks/solution);
  distinguish current upstream features from Wharf's supported subset

**Self-check:**

1. What do `task.toml`, `instruction.md`, and `tests/test.sh` each control?
   Which image source wins when `docker_image` and an agent Dockerfile exist?
2. What is reused on a warm run, and which image/container boundaries remain
   fresh? Why can shared base layers still leave substantial disk work?
3. Why cache an exact-version CLI payload separately from task environments?
   What evidence would support a claim about seconds saved?
4. Where is the agent patch retained, how does it reach grading, and why is
   that safer for reproducibility than sharing the agent workspace?
5. Which agent and verifier preparations acquire `P`, and when is it
   released? Does the Docker grader's patch replay hold that permit?
6. What do `nop` and `oracle` check? Why is Wharf's normal no-patch result
   not proof that it implements the `nop` baseline?

**Practice:** As a tabletop exercise, annotate the primer's two manifests:
one image-backed, one Dockerfile-backed. Draw the lifecycle with cache
identities, the retained patch, and preparation-gate boundaries. Then use
the smoke verifier Dockerfile, script, and grader to distinguish build-time
test bundling from runtime model/test patch application. No Docker run or
cache deletion is needed.

## Module 1: The workflow and its evidence

**Goal:** Understand how Sessions and RPI produced trustworthy AI-assisted work.

**Read:**

* [Development approach](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/RETRO.md#development-approach)
* [Benefits of Sessions and RPI](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/RETRO.md#benefits-of-sessions-and-rpi)
* One complete RPI set, such as the shared preparation gate
  [research](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/.github/tracking/2026-10-08-shared-preparation-gate-research.md),
  [plan](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/.github/tracking/2026-10-08-shared-preparation-gate-plan.md),
  [changes](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/.github/tracking/2026-10-08-shared-preparation-gate-changes.md),
  and [review](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/.github/tracking/2026-10-08-shared-preparation-gate-review.md)

**Self-check:**

1. What constraint does each RPI phase impose, and why does it matter?
2. Where does the research artifact separate evidence from assumption?
3. Why is "execution status" reported separately from "outcome" in a review?

## Module 2: Scaling up on one host

**Goal:** Explain why `N` and `P` are independent, and how to choose them.

**Read:**

* [Preparation concurrency is the stability control](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/RETRO.md#preparation-concurrency-is-the-stability-control)
* [Container execution and qualification hardening](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/RETRO.md#container-execution-and-qualification-hardening-0210-to-0230)
* [N=32 qualification checkpoint and Sessions 22 and 23](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/docs/session-log.md)

**Self-check:**

1. Which host resources does `N` mostly consume, and which does `P` protect?
2. Why is the cold run the right case for sizing `P`?
3. What does the gate telemetry show about the cost of a conservative `P`?
4. Why do verifier preparation and agent preparation compete for the same
   capacity even when all agents use cached CLI payloads?

## Module 3: Failure, retry, and recovery

**Goal:** Choose the correct retry boundary and preserve measurement identity.

**Read:**

* [Verifier-only retry research](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/.github/tracking/2026-10-08-verifier-only-retry-research.md)
* [Resume progress reconciliation research](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/.github/tracking/2026-10-11-resume-progress-reconciliation-research.md)
* Session 7, 12, and 13 entries in [Session retrospectives](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/RETRO.md#session-retrospectives)

**Self-check:**

1. Which failures are terminal, which retry the verifier, and which retry the whole trial?
2. Why must a shared build outlive one caller's cancellation?
3. Why is recovery planned as a pure function over validated events?

## Module 4: Isolation, identity, and safety

**Goal:** Keep agent and verifier environments separate and correctly identified.

**Read:**

* [Release 0.24.0](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/RETRO.md#release-0240)
* [Container version identity must not depend on the host CLI](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/RETRO.md#container-version-identity-must-not-depend-on-the-host-cli)
* Session 8 and 9 entries in [Session retrospectives](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/RETRO.md#session-retrospectives)

**Self-check:**

1. Why did Wharf replace host bind mounts with explicit container lifecycles?
2. Why must each ECR-backed trial build its own child image?
3. How does the adapter keep credential values out of arguments and logs?

## Module 5: Measurement you can defend

**Goal:** Compute and report benchmark results that survive scrutiny.

**Read:**

* [Benchmark evidence expanded without merging benchmark universes](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/RETRO.md#benchmark-evidence-expanded-without-merging-benchmark-universes)
* [Enterprise benchmark](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/RETRO.md#enterprise-benchmark-net-coverage-at-a-fraction-of-the-cost)
* [Enterprise summary](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/enterprise/data/summary.md)

**Self-check:**

1. Why does PASS@1 use the planned denominator?
2. Why are missing costs never imputed?
3. Why are DeepSWE and enterprise pass rates not interchangeable?

## Module 6: Qualification and release discipline

**Goal:** Know what evidence a release claim needs.

**Read:**

* [Release 1.0.0](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/RETRO.md#release-100)
* [Release 1.1.0](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/RETRO.md#release-110)
* [Release qualification](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/docs/release-qualification.md)
* [Failed runs need durable records before cleanup](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/RETRO.md#failed-runs-need-durable-records-before-cleanup)

**Self-check:**

1. Why can a package pass every metadata check and still fail?
2. What did the 1.0.0 review find that the passing test suite did not?
3. What should be recorded before deleting a failed job directory?

## Module 7: Learning from failure with an LLM coach

**Goal:** Practice engineering judgment by predicting, diagnosing, and
explaining a failure before reading its documented resolution.

**Read:**

* [Failure labs](failure-labs.md)
* [LLM coach prompts](llm-coach-prompts.md)
* [Failed runs need durable records before cleanup](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/RETRO.md#failed-runs-need-durable-records-before-cleanup)

**Self-check:**

1. What must you commit to before asking the coach for help?
2. How do you tell a useful coaching question from an answer that short-circuits learning?
3. What evidence would change your diagnosis, and what is still only a hypothesis?

**Practice:** Complete one failure lab without reading its debrief first.
Use the coach to examine your reasoning, not to write code or choose for you.

## Optional hands-on lab

On a Linux host with Docker, run Wharf on a small task set with a cold image
cache. Record a run once with a low `P` and once with a high `P` at the same
`N`. Compare preparation-gate wait times and any Docker errors, then write a
tracking entry for each run before deleting the job directories.

For a safer first exercise, complete the [failure labs](failure-labs.md) as
tabletop scenarios. Only run experiments in a disposable environment with
explicit resource limits; do not inject failures into a shared or production
benchmark run.
