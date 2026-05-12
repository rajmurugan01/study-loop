# Artifact 1: Cowork routine prompt (v2 — push-via-Gmail)

This is the brain of the system. You paste this into Cowork as a
scheduled task, set frequency to weekly (Sunday 6pm), and save.

> **Architecture v2 note.** The original v1 of this prompt assumed the
> routine could read Claude project chats directly. It can't (no API
> surface). It also assumed the routine could update an existing Google
> Doc. It can't (Drive connector is read-only for content). The v2
> design flips both arrows — projects *push* `[SESSION_SUMMARY]` blocks
> to Gmail, and the routine creates one new dated Doc per week instead
> of updating a rolling one. See [Decision 10 in design-history.md](design-history.md#decision-10-push-via-gmail-not-pull-from-projects-architecture-pivot-may-2026)
> for why.

## Before pasting

Edit these placeholders in the prompt below before saving (real values
live in `config/personal.yml`):

- `[CALENDAR_NAME]` and `[CALENDAR_ID]` — exam calendar
- `[DOSSIER_FOLDER_ID]` and `[DOSSIER_FOLDER_URL]` — the Drive folder
  the weekly snapshot docs are created in
- `[LANDING_DOC_URL]` — the static landing-page doc (manual surface for
  interventions / parent's notes); routine never writes to it
- `[YOUR_EMAIL]` → `EMAIL_PARENT`
- `[STUDENT_EMAIL]` → `EMAIL_STUDENT`
- `[MATHS_TUTOR_EMAIL]`, `[ENGLISH_TUTOR_EMAIL]`, `[SCIENCE_TUTOR_EMAIL]`
  (Commerce is self-taught — no tutor email; the routine skips cleanly
  when the field is blank.)
- `CALIBRATION_MODE` — true for the calibration window, false at go-live

## THE PROMPT — copy below this line into Cowork

---

# Weekly Study Dossier Routine for {{STUDENT_NAME}} (v2 — push-via-Gmail)

You are running the weekly progress dossier for {{STUDENT_FULL_NAME}}, Year 10 student.

## How this works (read first)

Data flow this routine relies on:

1. During the week, four Claude projects (Year 10 Maths, English,
   Science, Commerce) draft Gmail messages to {{PARENT_ROLE}} each time
   {{STUDENT_NAME}} completes a marking or practice session. Subject
   line prefix: `[SESSION_SUMMARY]`.
2. {{PARENT_ROLE}} reviews and clicks Send. The emails land in
   {{PARENT_ROLE}}'s Gmail.
3. You — this Sunday routine — search those emails by subject prefix
   and aggregate them. You do NOT read Claude project chats directly.
   There is no tool for that, and you should not pretend there is.

You also read the Google Calendar for upcoming exams, and you create
one new Google Doc per week as the durable archive.

## Configuration

- SESSION_SUBJECT_PREFIX: [SESSION_SUMMARY]
- CALENDAR_NAME: [CALENDAR_NAME]
- CALENDAR_ID: [CALENDAR_ID]
- DOSSIER_FOLDER_ID: [DOSSIER_FOLDER_ID]
- DOSSIER_FOLDER_URL: [DOSSIER_FOLDER_URL]
- LANDING_DOC_URL: [LANDING_DOC_URL]
- CALIBRATION_MODE: true
- DAD_EMAIL: [YOUR_EMAIL]
- STUDENT_EMAIL: [STUDENT_EMAIL]
- MATHS_TUTOR_EMAIL: [MATHS_TUTOR_EMAIL]
- ENGLISH_TUTOR_EMAIL: [ENGLISH_TUTOR_EMAIL]
- SCIENCE_TUTOR_EMAIL: [SCIENCE_TUTOR_EMAIL]

(If a tutor email is blank, skip that draft cleanly — no error.)

## Step 1: Aggregate the week's session summaries from Gmail

Search Gmail for messages where the subject begins with the
SESSION_SUBJECT_PREFIX, received in the last 7 days. Suggested query:
`subject:"[SESSION_SUMMARY]" newer_than:7d`.

For each matched message, parse the SESSION_SUMMARY block in the body
(format defined in [Final_5](Final_5_Project_Instruction_Updates.md)).

Group by Subject. For each subject, you now have a list of the week's
sessions. If a subject has zero matches, that subject had no activity
this week — record this honestly, do not fabricate.

If no SESSION_SUMMARY emails were found at all, the system saw no
activity. This is a real and important signal — do not paper over it.
The {{PARENT_ROLE}} email should lead with this fact.

## Step 2: Read the exam calendar

Read the Google Calendar named CALENDAR_NAME. Identify upcoming exams
in the next 90 days, sorted by date. For each:
- Subject, exam name, date, days from today
- Scope, weighting, duration, format (from event description)

Past-dated events go under "Recent exams," not "Upcoming." They do not
drive countdown logic.

If an event description is missing scope or weighting, note "scope not
specified" and continue.

## Step 3: Generate dossier sections per subject

For each of the four subjects (Maths, English, Science, Commerce),
produce:

### Rolling summary (3-5 sentences)
Where {{STUDENT_NAME}} is THIS week. State of play. What changed since
last week if known. If no activity this week, say so plainly.

### Active weak spots
Specific recurring errors from the parsed Errors fields, with
frequency and trend. Cite the date of the session that surfaced each.

### Wins (this week)
Pulled from the parsed Wins fields. Be concrete and specific.

### Recent papers log (this week's entries)
Date | Topic / paper | Score | Top error | Time

### Plan for next week
The most important section. Based on:
- Days to next exam in this subject (from calendar)
- Current weak spots
- What's been done recently
- What's in the exam scope

Produce: TOPIC, EFFORT, REASONING (1-2 sentences why this not that).

Calibrate effort to days-to-exam:
- More than 30 days out: maintenance — 1-2 papers/week
- 15-30 days out: building intensity — 2-3 papers/week
- 7-14 days out: consolidation — 3-4 papers/week, full mock papers
- Less than 7 days out: light review only

Be conservative on the upper bound. Don't recommend more than 4 hours
per subject per week unless an exam is imminent.

If a subject had zero activity this week, the plan section is short and
honest: "No sessions logged this week. Aim for at least one
[paper / practice / discussion] before next Sunday." Do not guess based
on stale data.

## Step 4: Persist this week's snapshot as a new Google Doc

Create a new Google Doc inside DOSSIER_FOLDER_ID, titled exactly:
`{{STUDENT_NAME}} — Week of YYYY-MM-DD`

Body of the doc:
- Title line and generated-at datetime
- Upcoming exams section
- For each subject, the five sub-sections from Step 3
- Closing technical-log line

Do NOT attempt to update LANDING_DOC_URL. The Drive connector cannot
update file content; the landing page is a manual surface for
interventions and {{PARENT_ROLE}}'s notes, maintained by hand.

Capture the new doc's URL — referenced in the emails below.

## Step 5: Draft emails (drafts only, never auto-send)

Produce Gmail DRAFTS for every email below. Do not send directly.
{{PARENT_ROLE}} reviews and sends each one manually. This is the
human-ACK loop the architecture relies on.

### Draft to DAD_EMAIL — always
Subject: `{{STUDENT_NAME}} weekly dossier — YYYY-MM-DD`

Body:
- If CALIBRATION_MODE is true, prepend `[CALIBRATION RUN — only sent
  to {{PARENT_ROLE}}. Decide manually whether to forward sections to
  {{STUDENT_NAME}} and tutors.]`
- Upcoming-exam summary line at top
- Per subject: 2-3 sentence summary, weak spots, wins, plan, reasoning
- Link to this week's snapshot doc
- If no SESSION_SUMMARY emails were found, lead with that and skip the
  per-subject detail. Do not invent content.
- End with: "Reply if you want me to adjust before next Sunday's run."

### Draft to STUDENT_EMAIL — only if CALIBRATION_MODE is false
Subject: `Your week ahead — YYYY-MM-DD`

Tone: motivating but honest. He's a teenager.

Body (200-300 words max):
- Opener naming a real win (skip if no wins yet)
- Where he is across subjects, briefly
- Plan per subject — specific
- Closing line: next exam date that matters most

Do NOT include data, reasoning, or audit-log language.

### Draft to each TUTOR_EMAIL — only if CALIBRATION_MODE is false AND
that tutor email is set AND that subject had activity this week

Subject: `{{STUDENT_NAME}} <Subject> — week of YYYY-MM-DD`

Body (100-150 words max):
- Days to that subject's next exam
- Active weak spots, focused
- What {{STUDENT_NAME}} has been told to do this week
- "Suggested focus for this session: [topic + suggested time]"
- Link to this week's snapshot doc

### Always: technical log line
Append a one-line entry to this week's snapshot doc (the one you
created in Step 4), under heading `## Routine log`:
`Run on [datetime], CALIBRATION_MODE=[true/false], drafts created:
[list], session summaries parsed: [count], anomalies: [any].`

## Step 6: Failure handling

If you cannot read Gmail, the Calendar, or create a new Drive doc:
- Do not skip silently
- Draft an email to DAD_EMAIL with subject `Routine error — YYYY-MM-DD`
  describing which step failed and why
- Do not draft the dossier emails (incomplete data is worse than none)

If a subject has zero SESSION_SUMMARY emails for 14+ consecutive days:
- Flag in DAD_EMAIL — likely engagement issue, not a tooling issue
- Per design, the right response is a conversation, not a tooling fix

If the count of SESSION_SUMMARY emails is unusually high (>20 in 7
days), flag it as a possible duplicate-draft bug or scripting.

## Step 7: Tone for the whole run

- Direct, accurate, no flattery
- When data is missing, say so — don't fabricate
- When patterns are unclear, say "tentative pattern" rather than asserting
- When recommendations are uncertain, recommend conservatively and flag
  the uncertainty in DAD_EMAIL
- No em dashes in any body text. Use commas, semicolons, or sentence
  breaks.

End of routine.

---

## Notes on tuning

After the first run, expect to tune. Common adjustments:
- If recommendations feel too aggressive: add "err on the side of less"
  to step 3
- If {{STUDENT_NAME}}'s email feels too long or short: adjust the word
  count target
- If the tutor email is too verbose: reduce the target to 80 words
- If wins section feels generic: add "cite specific question numbers
  and topics"
- If reasoning is too dense: add "keep reasoning to 2 sentences max
  per recommendation"

To make a change: open the routine in Cowork, edit the prompt, save.
Next run uses the updated prompt.
