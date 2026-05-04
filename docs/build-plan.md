# Build Plan — {{STUDENT_NAME}} Study System Rollout

## Context

The 6 design docs in [docs/](.) fully specify the system. [design-history.md](design-history.md) captures the *why*. Nothing has been built yet — the repo currently contains only `.claude/` and `docs/`.

This plan converts the spec into an executable rollout with an explicit **Definition of Done (DoD)** for every phase.

## Final architecture decisions (locked)

These supplement, and in places narrow, the choices in `design-history.md`:

1. **Single Max account on Dad's laptop.** {{STUDENT_NAME}} borrows the laptop to study. No Pro upgrade for {{STUDENT_NAME}}. No cross-account auth problem.
2. **Three separate email recipients.** {{STUDENT_NAME}} has his own email — his weekly study plan goes there directly. Dad's email gets the full dossier with reasoning. Tutor's email gets the per-subject brief.
3. **Shared-laptop hygiene** is a project-instruction concern: each subject project is {{STUDENT_NAME}}'s study space within the shared account, and Claude should gently redirect non-study questions to non-project chats.
4. **Routine scope is locked to the 3 named projects.** Non-project chats may belong to Dad's work and are out of scope. The routine prompt must say so explicitly.
5. **A how-to-use guide for {{STUDENT_NAME}}** is a new artifact, with a frank shared-account note: project chats are visible to Dad, non-project chats are also visible on the laptop, and if he wants a private Claude that's a fair conversation to have.
6. **Privacy:** repo is **private**. It contains identifiable information about a minor. Redact all personal information from artifacts before the first git commit (real names, school name, email addresses, exam dates, tutor identities). Use the **`personal.example.yml` + `personal.yml`** split — example committed with placeholders, real file gitignored.
7. **All 3 subjects from day one.** English/Science dossier sections will be thin until practice papers exist — accepted.
8. **No fixed go-live date.** Quality-gated: flip `CALIBRATION_MODE=false` only when 3 consecutive Sunday emails look right.

---

## Phase 0 — Repo prep + redaction (30 min)

**Goal:** Repo is a usable, private, redacted source of truth before any deployment.

**Steps:**
1. Convert `{{STUDENT_NAME}}_Final_1` … `{{STUDENT_NAME}}_Final_6` `.docx` files to `.md` siblings (the routine prompt and project instructions need to be copy-pasted from plain text, not Word).
2. **Sweep all `.md` files for personal data.** Replace with placeholders that match keys in `personal.example.yml`:
   - Names → `{{STUDENT_NAME}}`, `{{PARENT_NAME}}`, `{{TUTOR_MATHS_NAME}}`, `{{TUTOR_ENGLISH_NAME}}`, `{{TUTOR_SCIENCE_NAME}}`
   - School → `{{SCHOOL_NAME}}`
   - Emails → `{{EMAIL_PARENT}}`, `{{EMAIL_STUDENT}}`, `{{EMAIL_TUTOR_*}}`
   - Exam dates / specific topics → kept generic where possible; otherwise `{{...}}`
   - Calendar name, dossier doc URL → `{{CALENDAR_NAME}}`, `{{DOSSIER_DOC_URL}}`
3. Create `config/personal.example.yml` with all placeholder keys and dummy values, **committed**.
4. Create `config/personal.yml` with real values, **gitignored**. This is what Dad consults when filling Cowork prompt placeholders.
5. Add `.gitignore`: `.DS_Store`, `config/personal.yml`, `*.local.*`.
6. Add `README.md`: doc index, deploy order, where the live deployment lives (Cowork task name, dossier doc URL, calendar name — referenced by placeholder, real values in `personal.yml`).
7. `git init`, set remote to a **private** repo, first commit. Do not push until step 8 passes.
8. **Pre-push redaction audit:** `grep -r -i -E "<actual-name>|<actual-school>|@<actual-domain>"` against the working tree returns zero hits. (Do this with the real strings from `personal.yml`, not committed.)

**DoD:**
- [ ] All 6 `.docx` originals have `.md` siblings.
- [ ] No real names / school / emails / tutor identities anywhere in the tracked tree (audit grep returns clean).
- [ ] `config/personal.example.yml` committed; `config/personal.yml` exists locally and is gitignored.
- [ ] First commit pushed to a **private** remote.
- [ ] `README.md` documents the placeholder/config split so future-you knows where real values live.

---

## Phase 1 — Account & connector verification (15 min)

**Goal:** Confirm the platform actually supports the design before we build on it. Per design-history Decision 7: *verify Anthropic feature availability before committing to architecture.*

**Steps (from `{{STUDENT_NAME}}_Final_4` Phase 1):**
1. Verify Max plan active on Dad's account.
2. Verify Cowork access (cloud-scheduled routines visible in UI).
3. Connect Gmail, Google Docs, Google Calendar in Claude settings — all on Dad's Google account (calendar and dossier are owned by Dad, shared to {{STUDENT_NAME}} and tutors view-only).
4. Smoke test each connector: *"list my Google Calendars"*, *"list my recent Drive files"*, *"draft a Gmail to me"*. All three must succeed.

**DoD:**
- [ ] Max + Cowork confirmed on Dad's account.
- [ ] All three connectors return real data on a smoke test.
- [ ] Connector reauth cadence noted in `README.md` under "Maintenance".

**Stop condition:** If Cowork scheduling is unavailable, halt and reconsider architecture.

---

## Phase 2 — Calendar + Dossier doc (20 min)

**Goal:** The two external data surfaces the routine reads/writes exist and are correctly shared.

**Steps:**
1. Create Google Calendar **`{{CALENDAR_NAME}}`** on Dad's account.
2. Add at least one real upcoming exam using the exact format from `{{STUDENT_NAME}}_Final_6` (`[Subject] Exam: [Topic]`, four-line description: Scope / Weighting / Duration / Format).
3. Create Google Doc from `{{STUDENT_NAME}}_Final_2` template; name it the value of `{{DOSSIER_DOC_NAME}}` in `personal.yml`.
4. Set sharing: **{{STUDENT_NAME}} (his own email) view, tutors view, Dad edit.**
5. Record dossier doc URL and calendar ID in `config/personal.yml` (NOT in the committed README).

**DoD:**
- [ ] At least one calendar event in the exact title + 4-field description format.
- [ ] Dossier doc has all template sections; "manual" sections marked clearly so the routine won't overwrite them.
- [ ] Sharing verified by opening the doc on {{STUDENT_NAME}}'s logged-in account.
- [ ] URLs/IDs recorded in `personal.yml` only.

---

## Phase 3 — Three Claude projects + shared-laptop hygiene (45 min)

**Goal:** All three subject projects exist with instructions, knowledge, and the new shared-laptop guidance.

**Steps:**
1. Create projects on Dad's account: *Year 10 Maths*, *Year 10 English*, *Year 10 Science*.
2. Paste subject-specific instructions, **including the `[SESSION_SUMMARY]` block from `{{STUDENT_NAME}}_Final_5`** at the end.
3. **Append shared-laptop hygiene block** to each project's instructions:
   > This project is {{STUDENT_NAME}}'s study space within a shared Max account on Dad's laptop. Treat the user as {{STUDENT_NAME}} during this chat. If the user asks something that is clearly not study-related (work questions, personal admin, anything that sounds like Dad), gently say "this project is set up for {{STUDENT_NAME}}'s study — for that, open a fresh non-project chat" and stop. Do not surface or refer to non-project chats.
4. Upload knowledge:
   - **Maths:** textbook chapters in current scope, the 3 existing practice papers + marking, NESA Stage 5 syllabus reference.
   - **English:** current text(s), rubric/marking criteria, past essays + feedback.
   - **Science:** current chapter PDFs, syllabus reference.
5. Smoke test each project: subject question → Socratic stance fires; non-study question → redirect fires.

**DoD:**
- [ ] 3 projects exist on Dad's account; instructions pasted; `[SESSION_SUMMARY]` block + shared-laptop block both present in all 3.
- [ ] Maths project has all 3 papers + marking uploaded.
- [ ] Each project passes both smoke tests (Socratic on study, redirect on non-study).
- [ ] Fake end-of-session summary in each project renders the `[SESSION_SUMMARY]` block correctly.

---

## Phase 4 — Routine build & dry-run (30–60 min, then iterate)

**Goal:** The Cowork routine produces a Sunday email good enough to send to Dad only, and **only reads the 3 named projects**.

**Steps:**
1. Create new Cowork task. Schedule: weekly, Sunday 18:00 local.
2. Paste routine prompt from `{{STUDENT_NAME}}_Final_1`. Fill 7 placeholders from `personal.yml`. Set `CALIBRATION_MODE=true`.
3. **Add explicit project-scope clause** at the top of the prompt:
   > Scope of this routine: search ONLY the projects named "Year 10 Maths", "Year 10 English", "Year 10 Science". Do NOT read non-project chats — they may belong to Dad and are out of scope. If a connector returns chats outside these three projects, ignore them.
4. Recipient list in the prompt: Dad full dossier, {{STUDENT_NAME}} forward-looking plan to **his own email**, tutor brief to each tutor email.
5. Run as one-off. Inspect:
   - Calendar read? exam countdown correct?
   - `[SESSION_SUMMARY]` blocks parsed?
   - Dossier doc updated, "manual" sections untouched (verify with sentinel)?
   - **Only one email sent (to Dad), since `CALIBRATION_MODE=true`?**
   - **No non-project chat content leaked into the dossier?**
6. Iterate prompt; commit changes back to `{{STUDENT_NAME}}_Final_1.md` (redacted).

**DoD:**
- [ ] One-off run completes without errors.
- [ ] Dossier updated; sentinel intact in manual sections.
- [ ] Only Dad's email arrives.
- [ ] Sample the dossier text for any non-project content; none found.
- [ ] Exam countdown matches calendar.
- [ ] Prompt revisions committed (redacted).

**Stop condition:** Do not advance to Phase 5 until the one-off output is honest and project-scoped.

---

## Phase 5 — Calibration window (≥3 Sundays, quality-gated)

**Goal:** Three consecutive Sunday runs produce output that matches what you'd want sent to {{STUDENT_NAME}} and tutors. **No fixed deadline.**

**Steps:**
1. Each Sunday after 18:15, read Dad-only email.
2. Decide: forward {{STUDENT_NAME}}'s section to {{STUDENT_NAME}}'s email and tutor's section to tutor manually, or hold and tune.
3. Log each week's verdict in a "Calibration log" section in `README.md` (date, verdict, prompt edits, scope leakage check).
4. Watch for: tone too audit-y, recommendations too aggressive, empty sections, **non-project content leakage** (treat any leakage as a hard fail and re-tune).
5. As real `[SESSION_SUMMARY]` blocks accumulate from {{STUDENT_NAME}}'s actual usage, signal will improve. Do not flip the flag until 3 weeks show stable output **on real data, not fabricated.**

**DoD (gate to Phase 6):**
- [ ] ≥3 consecutive Sunday runs marked "would have sent as-is".
- [ ] Zero scope-leakage incidents in the last 3 runs.
- [ ] {{STUDENT_NAME}} has produced ≥1 `[SESSION_SUMMARY]` block per subject.
- [ ] No outstanding prompt-tuning todos.
- [ ] Recommendation calibration sanity-checked against the days-to-exam table.

**Stop / extend condition:** If {{STUDENT_NAME}} isn't engaging with the projects, the right move is a conversation, not flipping the flag (design-history *"don't fix this with tooling"*).

---

## Phase 6 — Go-live + {{STUDENT_NAME}} how-to-use guide (30 min)

**Goal:** Emails flow automatically to all three recipients; {{STUDENT_NAME}} understands the system and the privacy posture.

**Steps:**
1. Edit Cowork routine: `CALIBRATION_MODE=false`.
2. Run one-off; confirm 3 emails delivered (Dad's, {{STUDENT_NAME}}'s own email, tutor's).
3. **Write `docs/student-guide.md`** — {{STUDENT_NAME}}-facing how-to-use guide. Include:
   - What each project is for and how to use it (Socratic stance — don't expect answers handed over).
   - Always work inside the project chat, not a fresh chat — that's how the Sunday email knows what he did.
   - **Frank shared-account note:**
     > This is a shared account on Dad's laptop. Your project chats are visible to Dad — that's the point, it's how he can help you. Non-project chats on this laptop are also visible. If you want a private Claude for personal stuff, that's a fair conversation to have with Dad — ask him.
   - What the Sunday email will contain and what to do with it.
4. Walk through the guide with {{STUDENT_NAME}} in person; brief intro emails to tutors with link to dossier.
5. Update `README.md` with go-live date.

**DoD:**
- [ ] Confirmation run delivers all 3 emails to the right addresses.
- [ ] `docs/student-guide.md` exists, redacted (uses `{{...}}` placeholders), and committed.
- [ ] {{STUDENT_NAME}} has read the guide with Dad; tutors have been briefed.
- [ ] Go-live date logged in `README.md`.

---

## Phase 7 — Steady-state operations (ongoing)

**Cadences (from `{{STUDENT_NAME}}_Final_4` Phase 7):**
- **Weekly (5–10 min):** read Sunday email; act on or push back on top recommendation.
- **Monthly:** spot-check connector auth; skim dossier "manual" sections for drift; sample one dossier section for scope leakage.
- **Per term:** refresh project knowledge; confirm exam calendar against school portal.
- **Quarterly:** re-read `design-history.md`; ask whether any Decision should be revisited.

**DoD (system is healthy):**
- [ ] Weekly email read >80% of weeks.
- [ ] Recommendations have caused at least one change in tutor session or {{STUDENT_NAME}}'s plan in the last 4 weeks.
- [ ] Dossier doc reflects current term, not last term.
- [ ] No personal data has crept into the committed tree (periodic redaction grep).

**Failure-mode runbook:** in [design-history.md](design-history.md) "If something breaks, read this" + `{{STUDENT_NAME}}_Final_4` Phase 8.

---

## Critical files

**In repo (Claude Code edits, all redacted):**
- `README.md` — index, deployment pointers (placeholder-only), calibration log, go-live date.
- `.gitignore` — includes `config/personal.yml`.
- `config/personal.example.yml` — placeholder schema for all real values.
- `docs/{{STUDENT_NAME}}_Final_1_Routine_Prompt.md` — routine prompt, includes project-scope clause.
- `docs/{{STUDENT_NAME}}_Final_2_…_6_*.md` — `.md` siblings of the docx originals, redacted.
- `docs/student-guide.md` — new, {{STUDENT_NAME}}-facing how-to-use guide with privacy note.

**Local-only (gitignored):**
- `config/personal.yml` — real names, emails, URLs, tutor identities.

**Outside the repo (browser-only):**
- Google Calendar `{{CALENDAR_NAME}}` (Dad's account)
- Google Doc dossier (Dad's account)
- 3 Claude projects (Dad's account, with shared-laptop hygiene block)
- 1 Cowork scheduled task

## End-to-end verification (after Phase 6)

1. Add a new exam to `{{CALENDAR_NAME}}` mid-week.
2. Add a fresh `[SESSION_SUMMARY]` to one project chat.
3. **Open a non-project chat** on Dad's account with a unique sentinel string.
4. Trigger a one-off Cowork run.
5. Confirm: dossier updated, new exam in countdown, new session in per-subject log, all 3 emails arrive correctly framed, **sentinel from the non-project chat does NOT appear anywhere in dossier or any email.**

If any of those five checks fail, the matching phase's DoD has regressed.
