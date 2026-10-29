---
title: Frontier Engineering train-the-trainer pilot
description: Draft facilitator plan for discovering, implementing, and operating a URL-shortener specification
---

# Frontier Engineering train-the-trainer pilot

**Status:** Draft facilitator plan with pilot constraints agreed on
2026-10-28. The agenda and assessment remain proposals, not an approved
delivery commitment or a validated certification standard.

Originally drafted in Wharf at
[`fd9c194`](https://github.com/bartr/wharf/blob/fd9c194c11f7f4d5589cd26c7fbd0645d6d20fd1/docs/ttt-facilitator-plan.md);
the facilitator plan is now maintained in fe-core, separate from the benchmark
runner.

## 1. Purpose and design basis

Train facilitators to guide teams through three distinct stages of
AI-assisted software engineering:

1. **Getting to the spec:** turn an idea into evidence-backed requirements.
2. **Implementing the spec:** build and qualify a bounded release.
3. **Operating the spec:** observe the release, diagnose failures, and compare
   actual behavior with its acceptance criteria.

The pilot starts in a blank repository, installs the FE skills, and explores
an aka.ms-style URL shortener. Each stage retains the evidence needed by the
next. Learners practice the method first, then practice teaching it.

### Agreed direction from the planning discussion

The following captures the 2026-10-28 discussion, not independent research:

* The SDLC is taught as the three stages above.
* The first exercise uses a familiar scenario before bring-your-own-scenario.
* A blank repository and installed skills precede brainstorming.
* Requirements travel with their sources and decisions, not just a final spec.
* Gate A means ready to plan. For this pilot, planning means breaking the
  accepted spec into implementation sessions before coding begins.
* A specification template seeds implementation; an implementation template
  seeds operations.
* Emphasis and entry point vary by audience.
* Best practices remain, but teams can adapt their form to their own tools.
* Wharf is an example of what is possible, not a component of the offering.

### Agreed pilot constraints

The following decisions were confirmed on 2026-10-28:

| Area | Pilot decision |
|---|---|
| Duration | Two days |
| Reference architecture | Go redirect engine, separate from any admin/reporting UI; Next.js is the preferred portal candidate, not yet finalized |
| Cohort | 12 learners in pairs, with two facilitators |
| Infrastructure | Disposable, local-only labs; GitHub templates and Copilot access required; no public deployment |
| Packaging | Internal train-the-trainer pilot; learner and facilitator materials separated; no certification claim |

Go was selected for the redirect engine with runtime performance and Docker
image size in mind. These are selection goals, not measured results. Choose
and rehearse the storage implementation, Go version, and engine container
build before delivery; record measurements before making performance or
image-size claims.

Keep the redirect engine separate from any admin/reporting portal. Serving
a redirect must not require the portal to be running or proxy the request
through it. Next.js is the preferred candidate for the portal, not a final
stack commitment. Whether the pilot builds a portal, and which admin or
reporting functions it includes, remains a scope decision. Do not silently
add a UI or analytics to the bounded release. If a portal is included, define
its interface to the engine, access controls, and data ownership in the spec.

The two-day agenda, core feature cut, role rotation, drills, and assessment
below remain **proposals**. Rehearsal must validate that the agreed cohort
and facilitator coverage can meet the gates.

## 2. Outcomes

By the end, a trainer should be able to:

* Separate an outcome from a proposed solution and identify unsupported claims.
* Explain the different jobs of a BRD, PRD, and JSONL requirements ledger.
* Trace an acceptance criterion back to a source and forward to a test.
* Break an accepted spec into bounded sessions with dependencies, ownership,
  and verifiable outcomes before starting implementation.
* Facilitate a framed Sessions/RPI cycle without letting the LLM skip ahead.
* Diagnose a bounded operating failure using retained evidence.
* Distinguish application defects from bad requirements and bad measurements.
* Use an LLM as a questioning coach rather than an unquestioned authority.
* Adapt the workflow while preserving its evidence and validation principles.

Successful lab completion is not yet "Frontier Engineering Certified."
Certification needs an independently assessed unfamiliar exercise and pilot
evidence that the assessment measures useful transfer.

## 3. Audience-specific entry points

| Audience emphasis | Start with | Primary exercise | Handoff |
|---|---|---|---|
| Product, business, architecture | Blank repo and stakeholder brief | Discover and specify the problem | Approved spec plus evidence |
| Software engineering | Reviewed specification template, ready to plan | Break the spec into sessions, then implement and qualify a release | Runnable implementation plus evidence |
| Platform, SRE, operations | Reviewed implementation template | Diagnose, recover, and explain behavior | Incident record and verified recovery |
| Trainers | Experience all three | Teach back a selected transition | Facilitation feedback and adaptation plan |

These are emphasis choices, not rigid job-role boundaries. Every group sees
what it receives from the preceding stage and owes to the following one.

## 4. Preparation and roles

### Facilitator preparation

Before delivery:

* Rehearse the full route from blank repo through both template handoffs.
* Use Go for the reference redirect engine, separate from any admin/reporting
  UI. Select and record the tested Go version, storage implementation,
  engine container build, skill release, CLI/tool versions, and local runtime
  environment. If the pilot includes a portal, finalize and rehearse its
  stack and integration too. Do not select these mid-session.
* Prepare a synthetic stakeholder brief containing competing needs, explicit
  constraints, and a few intentional gaps. Label it as workshop material.
* Prepare reviewed reference specification and implementation templates.
  Keep the reference solution out of the learner's initial context.
* Test each drill, its recovery procedure, and its maximum resource use.
* Confirm GitHub repository/template permissions and AI tool access.
* Verify the local Go and container tooling on each learner machine before
  the pilot, including the rehearsed start, stop, and cleanup procedures.
* Set spending and resource limits; provide a local fallback for network or
  provisioning failures.
* Confirm that repositories contain no credentials, client information, or
  assessor-only material before they become templates.

Use the maintained [skills distribution instructions](https://github.com/frontier-engineering/skills/blob/main/docs/design.md#installing-with-gh-skill).
Record the installed release and any local modifications. Verify that the
assistant can discover the skills; files being present is not sufficient.

### Working roles

Use six learner pairs with two facilitators:

* **Learner/driver:** makes predictions and explains decisions before asking
  the LLM to implement.
* **Reviewer/observer:** checks sources, scope, and evidence; records drift.
* **Facilitator:** plays the stakeholder, probes reasoning, and enforces gates.

Rotate driver and reviewer roles within each pair. The planned coverage is
three pairs per facilitator; validate that capacity during rehearsal and the
pilot, rather than assuming AI coaching makes it sufficient.

### Delivery packaging

Package this as an internal TTT pilot, not a certification offering. Prepare
separate learner and facilitator materials:

* **Learner materials:** setup instructions, synthetic brief, exercises,
  gate checklists, and learner-facing assessments.
* **Facilitator materials:** reference solutions, assessor answer keys,
  drill causes and controls, recovery procedures, and scoring guidance.

Do not include facilitator-only files in learner template repositories or
their accessible history. Release reference fallbacks only at the relevant
handoff, without assessor answers or hidden drill causes. Verify the learner
view with learner-equivalent access before delivery.

## 5. Proposed two-day agenda

This pilot schedule is for a deliberately small, rehearsed implementation,
not a promise to build a production URL service in two days. If rehearsal
cannot satisfy the gates, revise scope or timing explicitly; do not silently
lower the acceptance criteria.

### Day 1: getting to the spec and planning implementation

| Time | Block | Exit evidence |
|---|---|---|
| 09:00-10:00 | Orientation, blank repo, skills, roles | Working environment and recorded tool/skill versions |
| 10:00-10:45 | Stage 1: discovery and stakeholder questions | Original brief, notes, assumptions, and gaps |
| 10:45-11:00 | Break | |
| 11:00-12:15 | Stage 1: BRD and PRD readiness | Business outcomes, source decisions, and readiness review |
| 12:15-13:00 | Lunch | |
| 13:00-14:00 | Stage 1: PRD and JSONL ledger | Consistent requirements and validated ledger |
| 14:00-14:45 | Gate A: ready to plan; spec-template handoff | Reviewed spec snapshot and provenance |
| 14:45-15:00 | Break | |
| 15:00-16:00 | Stage 2: research and session decomposition | Draft session map, dependencies, and specification gaps |
| 16:00-16:45 | Stage 2: session-map review and first-session fit check | Accepted session map and first session's RPI plan |
| 16:45-17:00 | Day 1 close and durable handoff | Saved artifacts, open questions, and restart instructions |

### Day 2: implementing, operating, and teaching back

| Time | Block | Exit evidence |
|---|---|---|
| 09:00-09:15 | Restore context and confirm the session map | Re-established first-session frame and checked prerequisites |
| 09:15-10:45 | Stage 2: implementation | Bounded code and acceptance tests |
| 10:45-11:00 | Break | |
| 11:00-12:15 | Stage 2: implementation and qualification | Runnable release and test evidence |
| 12:15-13:00 | Lunch | |
| 13:00-13:45 | Stage 2: review and bounded corrections | Truthful review and regression results |
| 13:45-14:15 | Gate B and implementation-template handoff | Qualified operating baseline |
| 14:15-14:30 | Break | |
| 14:30-15:30 | Stage 3: baseline, one operating drill, and recovery | Hypothesis, evidence, incident record, and recovery checks |
| 15:30-16:30 | Trainer teach-back and unfamiliar variation | Facilitation observations and feedback |
| 16:30-17:00 | Retrospective and next steps | Lessons, gaps, follow-up owners, and lab cleanup |

Day 2 provides 165 implementation focus minutes and 45 review minutes.
Re-establish the frame after the overnight handoff and lunch. Preserve
findings in files and follow the selected skill's phase handoff/context-reset
rules.

## 6. Stage 1: getting to the spec

### Exercise

Begin with "We want an aka.ms-style URL shortener," not a finished feature list.
This is a fictional learning service, not an implementation of Microsoft's
actual aka.ms requirements or infrastructure.

1. Save the original brief and brainstorming notes before generating documents.
2. Explore who needs the service, what outcome matters, and alternatives.
3. Use Design Thinking when the problem or users are uncertain. Workshop
   role-play is simulated evidence, not proof of real customer demand.
4. Use the BRD skill to extract business outcomes, measures, scope, risks,
   provenance, and gaps from the written notes.
5. Update the source notes with stakeholder decisions; do not let the LLM
   silently promote proposed assumptions into requirements.
6. Use the PRD skill's readiness review. Resolve blocking gaps before final
   generation.
7. Generate the human PRD and its JSONL ledger from the same source claims.

The BRD is not the JSONL generator. The PRD skill owns the machine-readable
requirements ledger and its validation.

### Proposed bounded release

Confirm or reject this feature cut in the source notes:

* Create a short link for a validated HTTP(S) destination.
* Resolve a short code and return the agreed redirect response.
* Define malformed input, unknown code, collision, and persistence behavior.
* State the redirect/cache policy and how it will be tested.
* Include enough telemetry and a runbook to verify those requirements.

Use only a disposable local environment and synthetic destinations. No real
aka.ms/bit.ly domains, customer links, or public deployment. A local exercise
without authentication is not a production-ready public redirect service.

Discuss identity, ownership, abuse handling, expiry, aliases, and click
analytics, but explicitly defer them or revise the scope and timing. If a
group includes click analytics, define what a "click" means and how caching,
bots, and previews affect measurement.

### Prompt

```text
Discovery only. Treat "an aka.ms-style shortener" as a proposed solution.
Help us identify users, outcomes, constraints, and competing options.
Separate simulated stakeholder evidence from assumptions. Preserve our raw
notes. Do not implement or turn an unanswered question into a requirement.
```

### Gate A: ready to plan

This gate accepts the specification as input to session planning; it does not
authorize coding or imply that implementation sessions are already defined.
Proceed to planning only when:

* Outcomes, non-goals, and the release boundary are explicit.
* Blocking contradictions and unconfirmed assumptions are resolved.
* Interface, persisted-data, security, and acceptance decisions are stated
  or deliberately delegated with a reason.
* Numerical targets have an agreed measurement method.
* PRD and ledger agree, with source paths/headings on claims.
* Every JSONL line parses; record IDs are unique, referenced IDs exist, and
  the selected skill's record structure and ordering are respected.
* A facilitator can trace one requirement to its source and describe how its
  acceptance criterion will be tested.

The [PRD skill](https://github.com/frontier-engineering/skills/blob/main/skills/prd/SKILL.md)
defines the detailed readiness and ledger contract; do not invent a
competing workshop format.

## 7. Stage 2: implementing the spec

Create a new repository from the accepted specification template. The first
activity is planning: break the accepted spec into implementation sessions,
not directly into code.

### Planning: break the spec into sessions

Produce and review an initial session map before coding:

* Account for all in-scope requirements and their acceptance criteria.
* Give each session a bounded outcome, scope, responsible owner, and
  verification approach, with requirement IDs for traceability.
* Identify dependencies, integration points, and the intended sequence.
* Detail the first executable sessions; refine later sessions as evidence
  arrives rather than pretending every detail is known up front.
* Return missing requirements, contradictions, or scope changes to the spec
  owner for a decision. Do not silently resolve them in the session map.

Review the map for coverage and feasible boundaries. Before starting code,
accept the map and the first session's RPI plan and fit check. A session map
decomposes the release; it does not replace the research and plan needed
inside each session.

### Responsibility at the handoff

For the pilot, learners propose the session map and the facilitator reviews
it, explicitly acting as the technical owner. This is a training arrangement,
not a requirement that facilitators own planning on real projects.

A proposed real-project default is that the product/business owner owns
outcomes, priorities, and acceptance, while the engineering lead or designated
technical owner owns session decomposition with the engineers doing the work.
Relevant operations and other specialists contribute to readiness and
verification. Teams may assign these responsibilities differently, but should
name who accepts the spec, the session map, and any subsequent scope changes.
An LLM can propose the breakdown; it does not own those decisions.

### Execute the sessions

For each session, establish the frame: goal, out of scope, failure condition,
and start time. Run Research, Plan, Implement, Review with durable artifacts,
crediting [Microsoft HVE Core](https://github.com/microsoft/hve-core) for RPI.
Verify APIs and repository patterns before code. At the fit check, split work
that does not fit, propose an explicit scope change, or use the reference
baseline if necessary; do not silently drop accepted requirements.
Review must test the actual runnable artifact, not just generated source.
Use the results to refine the remaining session map.

### Prompt

```text
Read the approved PRD, JSONL ledger, source decisions, and repository skills.
Gate A means ready to plan, not ready to code. Research the constraints and
break the accepted release into bounded implementation sessions. Map
requirements and acceptance criteria to sessions; identify owners,
dependencies, integration points, and verification. Report specification
gaps rather than inventing answers. Do not implement until the session map
is reviewed and the first session's RPI plan and fit check are accepted.
Apply the same RPI discipline to each subsequent session.
```

### Gate B: ready to operate

The implementation template contains:

* The inherited spec, sources, decisions, and provenance.
* The reviewed session map, including changes and requirement coverage.
* Runnable code with reproducible build/start instructions.
* Acceptance and regression tests tied to the agreed requirements.
* A qualified release snapshot and truthful review results.
* A runbook for start, stop, health checks, diagnostics, and recovery.
* Useful logs/metrics, a bounded test workload, and expected baseline behavior.
* A learner-safe baseline; safe, rehearsed drill controls and hidden causes
  retained separately in facilitator-only materials.
* Tracking evidence, session log, known limitations, and no credentials.

Do not pass the gate on "the LLM says it works." Demonstrate the artifact
from a clean instance of the generated repository.

## 8. Stage 3: operating the spec

Start another repository from the reviewed implementation template. First
verify normal behavior against the spec. Then assign one drill.

| Proposed drill | Learner question | Evidence to retain |
|---|---|---|
| Storage dependency unavailable in the disposable lab | Is this a failure of implementation, environment, or an unspecified requirement? | Timeline, logs, health/error behavior, recovery checks |
| Bounded request burst | Which agreed target is missed, and what is the bottleneck? | Workload settings, resource use, latency/error evidence |
| Redirect/cache-policy mismatch | Does the actual response honor the agreed policy? | Response headers, client behavior, acceptance-test result |

For the first pilot, storage unavailability is the simplest default drill.
Load and caching drills require a rehearsed measurement setup; they are not
mandatory features to add during the workshop.

Use a learner's own qualified implementation when its behavior supports the
drill. Otherwise use the reference template and record the switch. Any fault
control is lab-only, off by default, bounded, and must not contaminate the
normal release baseline. Do not plant exploitable vulnerabilities.

### Coaching sequence

1. Learner predicts behavior and writes an initial hypothesis.
2. Facilitator exposes the symptom without naming the cause.
3. Learner requests evidence and describes a discriminating test.
4. An LLM coach asks questions rather than supplying a diagnosis.
5. Learner chooses a recovery, then verifies it against the acceptance criteria.
6. Group compares its reasoning with the facilitator's evidence and records
   what it would do differently.

```text
Act as a questioning senior-engineer coach. Ask one question at a time.
Before giving your view, ask for my hypothesis, evidence, and confidence.
Distinguish observed facts from assumptions. Do not invent logs, fix the
service for me, or reveal the seeded cause before I commit to a diagnosis.
```

Retain an incident record before cleanup: configuration and version identity,
symptoms, timestamps, hypotheses, evidence, actions, recovery verification,
and unresolved questions. A fault injected by the facilitator is marked
synthetic. Never label an unverified recovery as successful.

If the spec is wrong or incomplete, record that finding and return to Stage
1; if implementation contradicts it, return to Stage 2. Operating the spec
includes challenging it, not just keeping the process running.

## 9. Template chain and reference fallbacks

Names below are placeholders; no repositories are created by this plan.

| Snapshot | Contains | Next use |
|---|---|---|
| `shortener-spec` | Skills, original sources, discovery/BRD, readiness review, PRD/JSONL | Create an implementation repo |
| `shortener-implementation` | Spec evidence plus qualified code, tests, runbook, operating baseline | Create an operations repo |
| Operations evidence snapshot | Implementation plus incidents, recoveries, lessons | Create a knowledge pack; optional third template |

GitHub templates copy files, not the original commit history. Commit the
evidence files before using a template, and record the source repo, release
tag, resolved commit SHA, installed skill versions, and known limitations in
each generated repo. Confirm the generated files correspond to that recorded
snapshot; do not assume creating from a moving default branch selects a tag.

Keep both cohort-produced and reviewed reference snapshots. At a failed gate:

1. Retain the cohort's work and the specific reason the gate failed.
2. Allow a bounded correction if the schedule supports it.
3. Otherwise continue from the reference template and record the handoff.
4. Debrief the gap later; do not count reference-template success as independent
   completion of the preceding stage.

This lets the next stage proceed without hiding how the cohort actually did.

## 10. Trainer teach-back and pilot assessment

In the teach-back, trainers facilitate rather than demonstrate their own speed.
Assign a colleague an unfamiliar variation, such as a new ownership requirement
or an unexplained error-rate increase. The trainer must ask useful questions,
preserve uncertainty, identify the correct SDLC stage, and choose a safe next
step without taking over the keyboard.

### Proposed observation rubric

| Dimension | Evidence |
|---|---|
| Discovery and traceability | Separates assumptions from evidence; traces requirement to source and test |
| Implementation judgment | Maintains scope, validates APIs, qualifies the actual artifact |
| Operating judgment | Tests hypotheses safely, retains evidence, verifies recovery |
| Facilitation | Coaches without giving answers; knows when and how to use a fallback |
| Transfer and adaptation | Handles an unfamiliar variation while preserving the principles |

Score each dimension: **0** no evidence, **1** completes with substantial help,
**2** independently demonstrates it, **3** explains trade-offs and can teach it.
Proposed pilot completion requires at least 2 in each dimension. The threshold
is provisional, not validated; a human assessor owns the judgment.

Save initial predictions separately from final answers. Track time spent,
coaching interventions, fallback use, and unsupported claims that were caught
or missed. Use the pilot to calibrate timing, facilitator capacity, rubric,
and later transfer/retention assessment. LLM feedback alone is not certification.

## 11. What remains to decide

| Decision | Proposed owner | Blocking when |
|---|---|---|
| Confirm the proposed core feature cut against the agreed two-day duration | Curriculum lead | Before rehearsal |
| Whether admin/reporting is in the pilot; if included, finalize the portal stack and engine interface | Curriculum lead and technical facilitator | Before rehearsal |
| Go version, storage, container build, local runtime, resource limits, costs, and access checks | Technical facilitator | Before rehearsal |
| Reference templates and safe drill implementation | Lab maintainer | Before delivery |
| Validate coverage for 12 learners in six pairs with two facilitators | Delivery lead | Before registration |
| Skill release and allowed client adaptations | Curriculum lead | Before publishing templates |
| Success measures and independent assessment process | Assessment lead | Before making certification claims |
| Template ownership, maintenance, branding, licensing notices, and learner/facilitator access separation | Program owner | Before pilot distribution |

Commercial packaging and external delivery are outside this internal pilot's
agreed scope; they require a separate decision.

## 12. Follow-on labs and materials

* **Helium:** a familiar service with an existing spec and data, suitable for
  brownfield implementation and deeper operating/performance exercises.
* **Bring your own scenario:** follows the known exercise; the client's own
  evidence becomes its own curriculum.
* **Wharf:** a reference case demonstrating the method under greater
  operational complexity, not software participants must deploy.
* [Wharf knowledge pack](knowledge/README.md), [failure labs](knowledge/failure-labs.md),
  and [LLM coach prompts](knowledge/llm-coach-prompts.md): examples to adapt,
  not mandatory client tooling.

The underlying [skills](https://github.com/frontier-engineering/skills) provide the reusable
method. This plan adds facilitation, handoff gates, and a pilot structure; it
does not replace their templates or claim that a finished curriculum,
reference implementation, or certification program already exists.
The RPI pattern is credited to [Microsoft HVE Core](https://github.com/microsoft/hve-core)
by the shared RPI skill.
