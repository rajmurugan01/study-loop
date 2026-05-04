# Study System Repo

Spec, prompts, and rollout plan for a weekly Cowork-driven study dossier
system. The system runs every Sunday: it reads three Claude project chats
plus a Google Calendar of upcoming exams, writes a shared Google Doc
dossier, and emails differentiated summaries to the parent, the student,
and each subject tutor.

This repo is **private** because it describes a system around a minor.
Real names, school, emails, exam dates, and tutor identities are kept in
`config/personal.yml` (gitignored). The committed tree uses `{{...}}`
placeholders that map to keys in `config/personal.example.yml`.

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
  personal.yml                 Real values (gitignored)
```

## Read order for setup

1. [docs/design-history.md](docs/design-history.md) — context and the
   non-negotiable design choices (Socratic stance, RAG over project
   knowledge, three-recipient email split, Cowork-as-deployment).
2. [docs/build-plan.md](docs/build-plan.md) — the executable plan.
   Every phase has an explicit Definition of Done. Don't skip phases.
3. [docs/Final_4_Setup_Instructions.md](docs/Final_4_Setup_Instructions.md)
   — step-by-step browser walkthrough that the build plan references.
4. [docs/Final_6_Calendar_Setup_Guide.md](docs/Final_6_Calendar_Setup_Guide.md)
   and [docs/Final_2_Dossier_Doc_Template.md](docs/Final_2_Dossier_Doc_Template.md)
   for the two external surfaces.
5. [docs/Final_5_Project_Instruction_Updates.md](docs/Final_5_Project_Instruction_Updates.md)
   for the `[SESSION_SUMMARY]` block that goes into each project's
   instructions.
6. [docs/Final_1_Routine_Prompt.md](docs/Final_1_Routine_Prompt.md) — the
   prompt to paste into Cowork. Fill placeholders from `config/personal.yml`.
7. [docs/Final_3_Email_Templates.md](docs/Final_3_Email_Templates.md)
   for the expected output shape during calibration.

## Final architecture decisions

These supplement [docs/design-history.md](docs/design-history.md) and were
locked in immediately before build:

- Single Max account on the parent's laptop. Student borrows the laptop.
- Three separate email recipients (parent, student's own email, tutor).
- Each project's instructions include a "shared-laptop hygiene" block
  redirecting non-study questions out of the project.
- The routine prompt explicitly scopes chat search to the three named
  projects only — non-project chats are out of scope.
- A separate `docs/student-guide.md` (created at Phase 6) gives the
  student a frank shared-account note.
- All personal data lives in `config/personal.yml` (gitignored). The
  committed tree is redacted.

## Live deployment pointers

Real URLs and IDs live in `config/personal.yml`. The committed tree only
references them by name:

| Surface              | Where it lives            | Config key            |
|----------------------|---------------------------|-----------------------|
| Exam calendar        | Google Calendar           | `CALENDAR_NAME` / `CALENDAR_ID` |
| Progress dossier     | Google Doc                | `DOSSIER_DOC_NAME` / `DOSSIER_DOC_URL` |
| Weekly job           | Cowork scheduled task     | `COWORK_TASK_NAME`    |
| Subject projects     | Three Claude projects     | (managed in Claude UI) |

## Maintenance

- **Weekly:** read the Sunday email (5–10 min). Act on or push back on
  the top recommendation.
- **Monthly:** spot-check Gmail / Docs / Calendar connector auth in
  Claude settings; tokens drift.
- **Per term:** refresh project knowledge (chapters, syllabus). Confirm
  exam calendar against the school portal.
- **Quarterly:** re-read [docs/design-history.md](docs/design-history.md)
  and ask whether any decision should be revisited.

## Calibration log

Append one row per Sunday during the calibration window
(see build-plan.md Phase 5):

| Date | Verdict (would-have-sent / hold) | Prompt edits | Scope leakage check |
|------|----------------------------------|--------------|---------------------|
|      |                                  |              |                     |

## Go-live

| Field                | Value |
|----------------------|-------|
| Go-live date         | (not yet) |
| `CALIBRATION_MODE`   | `true` |
| Recipients on go-live| parent, student, tutors (per `config/personal.yml`) |
