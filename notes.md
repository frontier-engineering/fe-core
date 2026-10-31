# Frontier Engineering: Working Notes

**Status:** Draft / review; organized from an LLM chat and forwarded notes.
**Source date:** October 30, 2026.
**Audience:** Enterprise architecture, platform engineering, digital
transformation leadership, and training partners.

> Frontier Engineering helps enterprises build internal engineering capability that turns domain expertise into reliable software—with AI. We demonstrate it alongside their teams, then enable their engineers and partners to repeat it.

These notes collect positioning ideas, possible architecture patterns, and
training proposals. They are not an approved charter, a verified research
report, or a statement that the proposed capabilities already exist.
Repeated chat responses, conversational praise, email headers, and personal
asides have been removed. Unverified claims are identified below.

## Request from Microsoft VP

- I just back to AMS now and have some headspace to really tuck into talking with you and your son's startup (Frontier-Engineering.ai) on helping us scale HVE. we finally have a mandate to move forward with this.. "this" being the opportunity to perhaps white label a small company to help us scale HVE upskilling to the broader set of MS customers. The recent Anthropic announce about thier Academy and upskilling 10k devs across customers and partners has RodK/LT twitching so the timing is PERFECT to propose a partnership that helps us scale. LMK if you feel we are still well positioned for this and then will get a small v-team kicked off to explore how we might best do this

## Summary

* **Frontier Engineer as internal FDE:** Explore a role that pairs software
  engineering capability with domain experts, turning rapid AI prototypes
  into governed, testable systems.
* **Dev Enclave:** Explore a standardized, centrally maintained Linux
  development environment that reduces local setup and support friction.
  Separate published-release consumption from active multi-user and
  multi-agent development.
* **Lightweight methodology:** Use repo-native skills and GitHub tooling,
  with sessions as the default outer loop and Microsoft's RPI workflow as
  the inner loop. Adapt the form without weakening the practices.
* **Training and assessment:** Explore hands-on certification and
  train-the-trainer delivery based on demonstrated engineering judgment,
  not prompt knowledge alone.
* **Evidence and learning:** Use Wharf as an example of measurable
  engineering outcomes and a source of case-study lessons, not as part
  of the FE offering.
* **Next step:** Choose a bounded Project Aspen pilot at Energy Co, define
  its controls and success criteria, and decide which training and
  positioning proposals to pursue.
*  Because some skills are open-source, do not build the business around restricting rights their licenses already grant. Your paid value is maintained curriculum, adaptation, delivery support, assessment, quality assurance, and authorized certification branding.

## 1. Naming and positioning

* Use **Project Aspen** for the initiative and associated SDLC/MVP discussion.
  This replaces the earlier project codename in the source chat.
* Use **Energy Co** as the anonymized enterprise example.
* Retain **Dev Enclave** as the proposed shared-environment term.
* **Frontier Engineer** as an internal Forward-Deployed Engineer (FDE) is a
  proposed positioning hook, not yet an adopted role definition.
* **Frontier Engineer Certified** is a proposed program name, not an
  established credential. Naming, trademark, and credentialing decisions
  remain open.

## 2. The problem: domain expertise and AI-driven shadow IT

Domain experts understand operational workflows but may need support with
version control, testing, security, state management, and production support.
AI tools make it easier to produce scripts, automations, and applications
before those disciplines are in place.

The opportunity is to capture useful experimentation without allowing
informal prototypes to become unmanaged production dependencies.

Risks raised in the discussion include:

* Undocumented business logic and unclear ownership.
* Missing data lineage, audit trails, and operational observability.
* Unmanaged credentials or inappropriate access to enterprise systems.
* Fragile state management and insufficient regression testing.
* Late security reviews, premature scaling, and compliance drift.

The proposed response is to pair builders with operators rather than rely
solely on prohibitions or centralized ticket queues. Prototypes provide
requirements input; they do not by themselves prove correctness, safety,
or production readiness.

## 3. Proposed role: Frontier Engineer / internal FDE

### Core mandate

Bridge domain operations and enterprise infrastructure:

1. Discover the actual workflow alongside domain experts.
2. Decide which steps remain deterministic, which may be agent-assisted,
   and where human approval is required.
3. Translate prototypes into bounded requirements and testable designs.
4. Integrate governed APIs, data access, and appropriate tool permissions.
5. Build version-controlled implementations with tests, observability,
   credential isolation, and accountable release decisions.
6. Extract reusable capabilities only after proving the local use case.

### Delivery model

```text
Domain experts / SMEs
  Operational context, prototypes, and validation
                  |
                  v
Frontier Engineer / internal FDE pod
  Discovery, hardening, governance, and integration
                  |
                  v
Enterprise platform
  Governed APIs, data services, security, and reuse
```

The relationship is two-way:

* **Field to platform:** Promote proven local solutions into reusable
  connectors, wrappers, audit pipelines, or other platform components.
* **Platform to field:** Bring enterprise security, governance, and
  observability into local workflows from the start.

Possible engagement forms discussed were delivery support ("done for you")
and hands-on team enablement. Their fit with FE's training-partnership scope
needs an explicit decision before they become offering language.

## 4. Methodology and the SDLC

Teach the SDLC as three stages:

| Stage | Application to the internal FDE proposal |
| --- | --- |
| Getting to the spec | Embedded discovery, workflow mapping, prototype evaluation, requirements, boundaries, and approval logic. |
| Implementing the spec | Build the narrowest viable slice, add controls and tests, and establish performance and cost baselines. |
| Operating the spec | Publish and support the capability, monitor outcomes, maintain ownership, and package proven components for reuse. |

The source draft used four activity phases: Discover & Align, Design &
Prototype, Build & Prove, and Package & Scale. Those activities are retained
in the three-stage framing above rather than introduced as a competing SDLC.

### Skill loop

```text
design-thinking -> brd -> prd -> sessions -> rpi
```

* Discovery and requirements skills turn ambiguous ideas into structured
  problem statements and specifications, leaving unresolved choices explicit.
* Sessions frame, bound, and close work at AI scale. They are the default
  decomposition approach, not the only permitted one.
* **Microsoft's Research, Plan, Implement, Review (RPI)** workflow supports
  verified work within a session.
* Version-controlled decisions, review evidence, tests, and release records
  carry durable context instead of relying solely on chat memory.

Best practices stay fixed; templates, skills, and decomposition formats can
be adapted to a team or client. Composability must preserve bounded work,
traceable decisions, human judgment, and verified outcomes.

## 5. Proposed architecture pattern: Dev Enclave

### Motivation and operator context

The source describes a dedicated HostFinch server with 32 threads, 128 GB
RAM, Ubuntu, and Docker, intended as a primary development box and for Wharf
tests. The proposed experiment is to support four or five business users,
or a programming pair, on centrally maintained compute.

Expected benefits to test:

* Less local environment setup and dependency drift.
* Easier troubleshooting in a known environment.
* Consistent toolchains for pairing and workshops.
* A repeatable deployment pattern for approved enterprise cloud or on-prem
  Linux hosts/VMs.

A shared host can reduce drift; it does not guarantee identical state,
eliminate onboarding friction, or provide isolation automatically.

### Two workspace tiers

| Tier | Users and purpose | Proposed boundaries |
| --- | --- | --- |
| Published-release consumption | Citizen developers and domain experts use stable releases, run validated workflows, and review outputs. | Restricted source/service access, controlled endpoints, and separation from active development state. |
| Engineering and collaboration | Engineers, programming pairs, and concurrent agents build, test, and evaluate changes. | Separate workspaces and runtime state, bounded resources, and explicit permissions. |

The operator clarified that citizen developers will usually use a published
release. Active multi-user and multi-agent development is the larger source
of state collisions, although consumption still needs access and data controls.

### Isolation and resource management to design

* Separate user homes, permissions, working copies, and agent workspaces.
* Isolate containers, networks, writable volumes, and test databases.
* Use CPU/memory limits or systemd slices/cgroups to bound contention.
* Allocate ports dynamically or route endpoints through a reverse proxy
  using distinct names or paths.
* Protect published releases from concurrent build and test activity.
* Use approved identity integration, audited access, and credential handling.
* Capture provisioning in repeatable cloud-init or equivalent blueprints.

These are design candidates, not a verified security architecture. Separate
logins, Docker contexts, or networks alone are not complete tenant-isolation
boundaries; shared Docker privileges and host access need explicit review.

Wharf's example workloads scale up on a single host. The Dev Enclave concept
does not imply a distributed benchmark runner.

### Train-the-trainer experiment

Use a Dev Enclave pilot to test workshop setup, support, isolation, and
repeatability. GSI facilitators could learn both the methodology and the
environment pattern in enterprise-approved infrastructure.

Measure onboarding time, support effort, concurrent workload behavior, and
release separation. Treat this as an experiment, not an established
"Enclave-as-a-Service" offering or a promise of frictionless training.

## 6. Training, certification, and continuous learning proposals

### Competency-based assessment

A proposed Frontier Engineer credential would assess the ability to take
an unstructured problem through specification, implementation, and operation.
Four curriculum pillars were suggested:

1. **Workflow mapping and bounding:** Identify deterministic steps,
   agent-assisted steps, dependencies, and human decision gates.
2. **Prototype hardening:** Apply version control, tests, state management,
   data boundaries, and credential isolation.
3. **Control-layer engineering:** Build audit trails, observability,
   review queues, and correction paths; validate any confidence measures.
4. **Enterprise reuse:** Turn proven local outcomes into maintainable
   platform capabilities without premature generalization.

Use practical artifacts and reviewable outcomes rather than LLM trivia.
Keep assessor answer keys separate from learner material at delivery.

### Git-driven learning platform

The chat proposes generating lessons, readings, and grading rubrics from
code evolution, session records, failures, and verifier evidence. Potential
benefits include ongoing learning and stronger architecture/debugging skills.

This remains a proposal. Automated grading validity, curriculum quality,
credential scope, and claims about developing senior-level competence need
validation.

### Partner discussion

The chat suggests a Microsoft/GSI train-the-trainer pilot or white-label
curriculum partnership to scale HVE upskilling.

Possible discussion points:

* Evidence-backed engineering practice rather than generic prompt training.
* Repo-native skills and tools with a low custom-runtime burden.
* A Dev Enclave experiment to reduce training setup friction.
* A bounded pilot with explicit responsibilities and measurable outcomes.

Decide whether to pursue a joint pilot or white-label delivery. Do not imply
that certification, an operational learning platform, or delivery capacity
is already established. Competitive academy references and claims about
leadership urgency in the source chat are unverified context, not evidence.

## 7. Wharf: case-study evidence and benchmark ideas

Wharf is an example of what is possible and a source of retained evidence.
The runner, benchmark extensions, and publication methodology belong in
the Wharf repository, not in the FE offering.

### Observations and assertions in the chat

The chat refers to:

* Fourteen enterprise-specific extensions / .NET tests.
* A plan initially split into 23 sessions versus a preferred scope of about 10.
* Run settings written as `K=4`, `N=64`, and `P=12`.
* Durable patches, trajectories, verifier evidence, and pass-to-pass (P2P)
  regression results.
* Easier onboarding when repository structure, intent, and tests carry context.

These references have not been checked against retained source evidence.
The settings lack enough context here to serve as reproducible instructions.
Session counts and onboarding experiences are observations, not guarantees
or productivity ratios.

### Proposed .NET and Java task expansion

Candidate .NET areas: asynchronous pipelines, Entity Framework Core queries
and migrations, Minimal API boundaries, and dependency-injection lifecycles.

Candidate Java areas: Spring Security multi-tenancy, WebFlux pipelines,
Hibernate/JPA transaction boundaries, and Kafka offset management.

Potential task-pack requirements:

* Reproducible runtimes and language-specific SDK/JDK images.
* Tests that establish the requested fix and P2P regression coverage.
* Retained patches, trajectories, verifier outputs, and cost/performance data.
* Clear provenance and licensing for tasks adapted from repository issues
  or pull requests.

Choose between adapting suitable open-source tasks and authoring tasks from
known enterprise failure modes. Verify claimed DeepSWE contracts before
treating them as a standard.

Any published Wharf claims should link to commit-pinned retained evidence.
No benchmark evidence pins were supplied in the source chat.

## 8. Lightweight, repo-native tooling

The intended positioning is a **Minimum Viable Standard (MVS)** built around
curated GitHub Copilot primitives, inspectable Markdown skills, and the
GitHub CLI rather than a new FE runtime.

* FE Core owns methodology, curriculum, training plans, and evidence pointers.
* FE skills owns installable, reusable skills; link to them rather than copy.
* Awesome Copilot is a discovery and curation foundation.
* Microsoft HVE Core is a methodology/tooling reference and the source to
  credit for RPI, not an FE installation dependency.
* Context-first is the origin of the sessions methodology.
* Review upstream changes and publish deliberate, versioned updates rather
  than silently changing a project's or cohort's skills.

The goal is adaptability as models and tools evolve, not a claim that the
methodology is future-proof or that lightweight tooling removes legal review.

## 9. Claims requiring verification before publication

| Source-chat claim | Treatment |
| --- | --- |
| HVE Core is necessarily global, monolithic, or forces identical rules on every project. | Unverified characterization; inspect actual supported installation and scoping before comparison. |
| HVE Core's licensing creates unusual legal barriers, while FE curation is entirely unencumbered. | Unverified legal conclusion; check licenses, attribution, trademarks, and redistribution obligations. |
| A fixed percentage of workshop time is lost to setup, or onboarding friction is eliminated. | No supporting measurement supplied; establish a pilot baseline. |
| AI tools inherently produce engineers without architecture or debugging competence. | Broad generalization; assess actual skills and outcomes. |
| Existing curricula, benchmark packs, and learning infrastructure are ready for immediate large-scale rollout. | Confirm readiness and capacity before using in a partner pitch. |
| New models will make current frameworks obsolete on a predictable schedule. | Speculation; retain adaptability as a design goal without forecasting guarantees. |
| Dev Enclaves eliminate shadow IT or automatically ensure compliance. | Proposed mitigation, not a guarantee; define and validate controls. |

## 10. Open decisions and next actions

1. Decide whether the internal FDE framing becomes part of FE's role narrative.
2. Define a single high-impact Project Aspen use case at Energy Co with a
   cross-functional pod, bounded MVP, and explicit acceptance criteria.
3. Resolve workflow boundaries, agent permissions, data access, and human
   approvals before committing to agentification.
4. Design and test Dev Enclave consumption/development separation, including
   multi-user and multi-agent contention.
5. Select the train-the-trainer pilot scope and measure setup/support outcomes.
6. Decide credential scope: client-internal assessment or a broader program.
7. Define and validate any git-driven learning or automated assessment pilot.
8. Verify Wharf observations and collect commit-pinned evidence for publication.
9. Decide benchmark task sourcing in Wharf, separately from FE training scope.
10. Choose the next writing deliverable: whitepaper/blog, role definition,
    Project Aspen charter material, or training module.

The original draft proposed a governed MVP within a compressed quarterly
cycle. Treat that as a planning hypothesis until scope, capacity, and
approvals are known.

## References

* [FE Core overview](README.md)
* [Draft train-the-trainer facilitator plan](docs/ttt-facilitator-plan.md)
* [Wharf knowledge pack](docs/knowledge/README.md)
* [FE skills](https://github.com/frontier-engineering/skills)
* [FE skills installation and update policy](https://github.com/frontier-engineering/skills/blob/main/docs/design.md)
* [Microsoft HVE Core](https://github.com/microsoft/hve-core)
* [Context-first](https://github.com/context-first/core)
* [GitHub Awesome Copilot](https://github.com/github/awesome-copilot)
* [Wharf](https://github.com/bartr/wharf)

Repository links above are orientation references, not commit-pinned evidence
for the chat's benchmark or comparative claims.

### Job description references

User-supplied job description (JD) references for comparison with the proposed
Frontier Engineer / internal FDE role. Titles are supplied by the user or
inferred from the linked URL. Local reference summaries were prepared on
October 30, 2026, rather than reproducing the full postings. The Fluidstack
posting bodies were retrieved directly; the OpenAI summary is search-based
because direct retrieval returned HTTP 403. Postings may change or close.

#### Fluidstack: Decision Engineer

* [Decision Engineer Infrastructure](https://fluidstack.io/jobs/4d9e6d9f-d1f5-4e56-b743-a0f082fb7217)
  ([local summary](docs/jd-fluidstack-infrastructure.md))
* [Decision Engineer Compute Production](https://fluidstack.io/jobs/1fa894eb-09ce-478f-b440-aae45aa4953e)
  ([local summary](docs/jd-fluidstack-compute-production.md))
* [Decision Engineer Business Operations](https://fluidstack.io/jobs/e2a1dad4-f2dd-4cd6-b19e-c50bcb51fe0d)
  ([local summary](docs/jd-fluidstack-business-operations.md))
* [Decision Engineer Manufacturing & Supply Chain](https://fluidstack.io/jobs/717a792e-bd03-40c3-acf6-48e360a9034b)
  ([local summary](docs/jd-fluidstack-manufacturing-supply-chain.md))

#### OpenAI: GTM Growth Engineering

* [Software Engineer, GTM Growth Engineering (San Francisco)](https://openai.com/careers/software-engineer-gtm-growth-engineering-san-francisco/)
  ([local summary](docs/jd-openai-gtm-growth-engineering.md))
