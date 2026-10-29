# Frontier Engineering Core

The FE methodology and training hub: evidence-backed specification,
implementation, and operations. Practices can be adapted to a team's needs
while preserving traceable decisions, human judgment, and verified outcomes.

## Methodology foundations

FE Core builds on two external methodology sources. They are referenced here
rather than copied, so each remains maintained in its own repository.

* [Microsoft HVE Core](https://github.com/microsoft/hve-core): tooling and the
  Research, Plan, Implement, Review (RPI) workflow for getting verified output
  from an AI assistant on one task. Credit Microsoft when referencing RPI.
* [Context-first](https://github.com/context-first/core): the origin of the
  sessions methodology, the outer loop that frames, bounds, and closes work,
  with one RPI cycle typically running inside each session. Start with
  [Sessions, not stories](https://github.com/context-first/core/blob/main/methodology/sessions-not-stories.md)
  and [Sessions and RPI](https://github.com/context-first/core/blob/main/methodology/sessions-and-rpi.md).

Sessions are a default way to decompose work at AI scale, not the only way.
Teams can substitute their own planning and delivery practices while keeping
evidence-backed decisions, bounded work, human review, and verified outcomes.

## Training and case studies

* [Draft train-the-trainer facilitator plan](docs/ttt-facilitator-plan.md):
  a URL-shortener exercise spanning all three stages, with template handoffs,
  facilitation guidance, and provisional assessment criteria.
* [Wharf knowledge pack](docs/knowledge/README.md): evidence-derived lessons,
  a study guide, assessments, failure labs, and LLM coaching prompts.
* [Helium / movies retrospective](https://github.com/context-first/core/blob/main/experiments/movies-bartr/RETRO.md),
  in context-first: an existing-spec example for the implementation and
  operations stages, complementary to the URL-shortener discovery exercise.

## Related projects

* [FE skills](https://github.com/frontier-engineering/skills): installable,
  reusable skills and their distribution instructions.
* [HVE Core](https://github.com/microsoft/hve-core) and
  [context-first](https://github.com/context-first/core): the methodology
  sources described above.
* [Wharf](https://github.com/bartr/wharf): the benchmark runner and source
  evidence for the Wharf case study. Wharf is an example, not part of the
  workshop offering.

The training documents were moved from Wharf without moving its retrospective,
tracking evidence, benchmark data, or benchmark publication methodology.
Case-study links to that evidence are pinned to the source commit; the
knowledge pack records its provenance.
