# fe-core repository guidelines

## Purpose and boundaries

fe-core is the Frontier Engineering (FE) methodology and training hub. FE is
a training partnership focused on HVE upskilling for Microsoft Frontier
Company and its global system integrators. This repository holds the method,
curriculum, facilitator plans, and pointers to evidence.

Keep each concern in its owning repository:

| Repository | Role | Relationship |
|---|---|---|
| `frontier-engineering/fe-core` | Methodology, curriculum, training plans | This repository |
| `frontier-engineering/skills` | Installable, reusable skills | Nested clone at `skills/`, ignored by Git; link, don't copy |
| `bartr/wharf` | Benchmark runner and case-study evidence | Link with commit-pinned URLs |
| `context-first/core` | Origin of the sessions methodology, experiments, Helium retro | Referenced, not copied; may be moved here later |
| `microsoft/hve-core` | RPI workflow and Copilot tooling | Referenced; always credit Microsoft for RPI |

Do not copy content from referenced repositories without an explicit decision.

## Positions to preserve

* Wharf is an example of what is possible, not part of the FE offering.
* Best practices are fixed; their form can be adapted to the team or client.
* Sessions are the default way to decompose work at AI scale, not the only way.
* Teach the SDLC as three stages: getting to the spec, implementing the spec,
  and operating the spec. Audiences may emphasize different stages.
* Never describe Wharf as distributed; it scales up on a single host.
* Separate operator observations from retained evidence, and label drafts and
  proposals as such. Small-sample productivity ratios are observations, not
  guarantees.
* Keep assessor answer keys separate from learner material at delivery.

## Workflow

* Make changes on the `dev` branch; leave `main` unchanged unless asked.
* Commit and push only when asked. Include the trailer
  `Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com>`.
* Store Research, Plan, Implement, Review artifacts under `.github/tracking/`.
* Git authentication uses the repository-local setting
  `credential.username=frontier-engineering` (also set in `skills/`). Never
  embed tokens in remote URLs or print credential values.
