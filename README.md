# Weekly Study Dossier — a Claude-driven system for parents

A weekly Sunday-evening routine that turns your child's actual study
sessions into three differentiated emails: a full dossier with reasoning
to you, a focused brief to each subject tutor, and a motivating
forward-looking plan to your child.

It runs on Anthropic's [Cowork](https://www.anthropic.com/) cloud
routines, reads three Claude projects (one per subject) plus a Google
Calendar of upcoming exams, updates a shared Google Doc dossier, and
sends the emails — all on Sunday evening, with no laptop required after
setup.

> Built for one specific Year 10 student in Australia. The whole repo is
> a redacted template — every name, email, school, and date is a
> `{{...}}` placeholder. Fork it, drop in your own
> [config/personal.yml](config/personal.example.yml), and adapt.

## Architecture v2 — push-via-Gmail (May 2026)

The original design assumed the Sunday Cowork routine could read Claude
project chats directly and update a rolling Google Doc. The first
real-run dry test surfaced both as false: there's no API surface for
project-chat reads from Cowork, and the Drive connector is read-only
for content. The architecture pivoted:

- **Capture (during the week):** each subject's project instructions
  tell Claude to draft a Gmail to the parent at end-of-session, with
  subject prefix `[SESSION_SUMMARY]` and the structured summary block
  in the body. Parent reviews the draft (~10 sec) and clicks Send.
- **Aggregate (Sunday routine):** the routine searches Gmail by
  subject prefix instead of reading project chats. Same data, different
  source.
- **Persist (Sunday routine):** the routine creates one new dated doc
  per week inside a shared Drive folder, instead of updating a rolling
  doc. The folder is the chronological archive.
- **Deliver (Sunday routine):** the routine produces Gmail *drafts* for
  parent / student / tutor, never auto-sends. Parent ACKs each.

Why this is in the README and not just the design history: it's the
single biggest change between v1 and v2 of the spec, and anyone forking
this repo needs to know which architecture they're building. The full
write-up is [Decision 10 in design-history.md](docs/design-history.md#decision-10-push-via-gmail-not-pull-from-projects-architecture-pivot-may-2026).

## Why this exists

A short version: I started by building one practice exam paper. Marking
it surfaced patterns. A second harder paper confirmed them. A third
targeted the patterns. By then it was clear we needed something durable
that didn't depend on me sitting next to him every Sunday — and that the
tutor and my son both needed visibility, but in different forms.

The full story, including all the design choices and the things I got
wrong, is in [docs/design-history.md](docs/design-history.md).

## What you need

- An Anthropic Max subscription with Cowork enabled
- A Google account (for Calendar, Docs, Gmail)
- Roughly 90 minutes for the initial setup
- A willingness to spend ~3 weeks calibrating before going live to your
  child and tutor

## Repository layout

```
docs/
  design-history.md            Why the system is shaped the way it is
  build-plan.md                Phased rollout with Definition of Done per phase
  Final_1_Routine_Prompt.md    Cowork prompt — the heart of the system
  Final_2_Dossier_Doc_Template.md
  Final_3_Email_Templates.md
  Final_4_Setup_Instructions.md
  Final_5_Project_Instruction_Updates.md
  Final_6_Calendar_Setup_Guide.md
config/
  personal.example.yml         Placeholder schema (committed)
  personal.yml                 Real values (gitignored — you create this)
LICENSE                        MIT — fork freely
```

## Quick start

1. **Fork or clone** this repo.
2. **Copy** `config/personal.example.yml` → `config/personal.yml` and
   fill in your real values. `personal.yml` is gitignored — your data
   never leaves your machine.
3. **Read** [docs/design-history.md](docs/design-history.md) first.
   The non-negotiable design choices (Socratic stance, RAG over project
   knowledge, three-recipient email split) are explained there. Don't
   weaken them without understanding why they exist.
4. **Follow** [docs/build-plan.md](docs/build-plan.md) phase by phase.
   Every phase has an explicit Definition of Done — don't skip ahead.

## Read order during setup

1. [docs/design-history.md](docs/design-history.md) — context and
   non-negotiable design choices.
2. [docs/build-plan.md](docs/build-plan.md) — the executable plan,
   8 phases (0–7), each with DoD.
3. [docs/Final_4_Setup_Instructions.md](docs/Final_4_Setup_Instructions.md)
   — step-by-step browser walkthrough that the build plan references.
4. [docs/Final_6_Calendar_Setup_Guide.md](docs/Final_6_Calendar_Setup_Guide.md)
   and [docs/Final_2_Dossier_Doc_Template.md](docs/Final_2_Dossier_Doc_Template.md)
   for the two external surfaces.
5. [docs/Final_5_Project_Instruction_Updates.md](docs/Final_5_Project_Instruction_Updates.md)
   for the `[SESSION_SUMMARY]` block that goes into each project's
   instructions.
5b. [docs/project-instructions.md](docs/project-instructions.md) — the
   three full subject-instruction blocks (Maths / English / Science),
   ready to paste into each project. Bundles the Socratic stance, RAG
   grounding, session-summary format, and shared-laptop note.
6. [docs/Final_1_Routine_Prompt.md](docs/Final_1_Routine_Prompt.md) —
   the prompt to paste into Cowork. Fill placeholders from
   `config/personal.yml`.
7. [docs/Final_3_Email_Templates.md](docs/Final_3_Email_Templates.md)
   for the expected output shape during calibration.

## Architecture decisions you should know up front

- **Single Max account on the parent's laptop.** The child borrows the
  laptop. No second subscription needed.
- **Three separate email recipients** (parent, child, each tutor) with
  three different framings of the same data.
- **Each project has shared-laptop hygiene built into its instructions**
  — it gently redirects non-study questions out of the project chat.
- **The routine prompt scopes search to the three named projects only**
  — non-project chats on the same account are out of scope.
- **A separate `docs/student-guide.md`** (created at Phase 6) gives the
  child a frank shared-account note: "your project chats are visible to
  your parent — that's the point."
- **Personal data lives in `config/personal.yml` (gitignored).** The
  committed tree is fully redacted.

## Maintenance cadence

- **Weekly (5–10 min):** read the Sunday email; act on or push back on
  the top recommendation.
- **Monthly:** spot-check Gmail / Docs / Calendar connector auth in
  Claude settings; tokens drift.
- **Per term:** refresh project knowledge (chapters, syllabus). Confirm
  exam calendar against the school portal.
- **Quarterly:** re-read [docs/design-history.md](docs/design-history.md)
  and ask whether any decision should be revisited.

### Connector verification log

| Date | Connectors verified | Notes |
|------|---------------------|-------|
| 2026-05-04 | Gmail, Google Docs, Google Calendar | Phase 1 smoke tests passed (list calendars, list recent Drive files, draft Gmail). Re-check on first routine failure or quarterly. |

## Calibration log

Append one row per Sunday during the calibration window
(see [docs/build-plan.md](docs/build-plan.md) Phase 5):

| Date | Verdict (would-have-sent / hold) | Prompt edits | Scope leakage check |
|------|----------------------------------|--------------|---------------------|
|      |                                  |              |                     |

## Adapting this for your family

The placeholder pattern means almost everything is parameterised. The
parts most likely to need real changes:

- **Subjects.** This template assumes Maths / English / Science. If
  your child takes different subjects, rename the three Claude projects
  and adjust subject-specific bits of the routine prompt
  ([Final_1](docs/Final_1_Routine_Prompt.md)).
- **Curriculum.** This was built for Australian NESA Stage 5
  (Year 10). Other curricula will have different notation, marking
  conventions, and rubric language — upload your child's actual
  textbook and rubric to project knowledge so Claude grounds in the
  right material.
- **Error taxonomy.** [Final_5](docs/Final_5_Project_Instruction_Updates.md)
  defines seven error categories. If your child's errors look
  different, edit that taxonomy *before* the routine starts collecting
  data, otherwise patterns won't aggregate cleanly.
- **Tone.** Tutor and child emails have specific tone guidance in
  [Final_1](docs/Final_1_Routine_Prompt.md). Tune to your family.

## Contributing

Pull requests welcome — especially:

- Adaptations for other curricula (UK GCSE, US APs, IB, etc.)
- Improvements to the error taxonomy
- Failure modes you encountered that aren't covered in
  [docs/design-history.md](docs/design-history.md) "If something breaks,
  read this"

## License

[MIT](LICENSE). Fork freely.
