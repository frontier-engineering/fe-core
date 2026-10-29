---
title: "Assessment 1: Foundations"
description: Knowledge check on the lessons recorded in Wharf's evidence
---

## Instructions

Answer all sixteen questions. Short answers should be two to four sentences.
Open-book use of the repository is allowed. The passing score is 80%.

## Multiple choice

**1.** A K=4 run of 14 tasks completes with 50 passing trials, 3 failed
trials, 2 verifier-timeout trials scored zero, and 1 cancelled trial. What is
PASS@1?

* A. 50 / 53
* B. 50 / 55
* C. 50 / 56
* D. 14 tasks solved / 14

**2.** A verifier times out after the agent produced a patch. What should Wharf do?

* A. Rerun the whole trial, including the agent.
* B. Retry only the verifier, reusing the exact agent patch and cost.
* C. Mark the trial as a measured failure immediately and never retry.
* D. Add a new logical trial to replace the failed one.

**3.** An agent exits cleanly but produces no patch. How is it classified?

* A. Success, because the exit code was zero.
* B. Retryable infrastructure failure.
* C. Terminal unfinished result.
* D. Excluded from the denominator.

**4.** Which value should determine the preparation concurrency `P` used for every run?

* A. The warm-run value, since most runs are warm.
* B. The processor count.
* C. The value that is stable on a cold run.
* D. The same value as `N`.

**5.** Three trials have known costs and one has no cost evidence, but the
batch invoice is available. What should the export do?

* A. Divide the invoice evenly across all four trials.
* B. Leave the missing cost null and report cost coverage.
* C. Record the missing cost as zero.
* D. Drop the trial without cost from the export.

**6.** A Debian package passes checksum, SBOM, and version-metadata checks.
What is still required?

* A. Nothing; those checks prove the package.
* B. A signature.
* C. Executing the extracted package and checking its version.
* D. A larger SBOM.

## Short answer

**7.** Explain why `N` and `P` are separate settings, and name the resources each mainly protects.

**8.** Why must one caller's cancellation not cancel a shared derived-image build?

**9.** Why should a phase slot be released before retry evidence is written?

**10.** Why did the enterprise suite replace reflection and API-shape checks?

**11.** Why can't the enterprise PASS@1 be compared directly with the DeepSWE PASS@1?

**12.** What should be recorded before deleting a failed job directory, and why?

**13.** You find `task.toml`, `instruction.md`, and `tests/test.sh` in a task.
Explain their different jobs. If the task also contains both
`environment.docker_image` and `environment/Dockerfile`, which agent
environment source does Wharf use?

**14.** Explain the difference between reusing a task image, a versioned CLI
payload, and a composed agent image. What must still be created for each
attempt, and why is "the cache always saves 20 seconds" an unsupported claim?

**15.** Trace the agent's output into a separate verifier: name the retained
patch artifacts, explain where the model patch and evaluator test patch
are applied, and identify the resulting grading evidence. Why is the
agent's zero exit code not enough?

**16.** What do Harbor's `nop` and `oracle` baselines establish about a task?
Does a clean no-patch exit in Wharf demonstrate that its `nop` baseline
passed? Explain the distinction.
