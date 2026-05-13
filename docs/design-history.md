# {{STUDENT_NAME}} Study System — Design History

Why the system is shaped the way it is. Read this before changing it.

Captured: May 2026

Context: written at the end of the design conversation, before transitioning to Claude Code for repo management. Documents the key decisions made, alternatives considered, and reasoning. Future-you (or {{STUDENT_NAME}}, or whoever maintains this) should read this before making structural changes.

## The starting point

This started as a single request: build a 45-minute Year 10 Maths exam paper for {{STUDENT_NAME}} on a specific chapter range from his textbook. Nothing more. Nothing about study systems, dossiers, scheduled emails, or multi-subject coverage. Just one paper.

It expanded over the conversation as patterns emerged from real data — {{STUDENT_NAME}}'s actual marked papers, his actual error patterns, and Dad's evolving sense of what would actually help.

The journey roughly went:

- One practice paper → marked {{STUDENT_NAME}}'s attempt → spotted error patterns
- Harder paper targeting weak spots → marked → patterns confirmed and refined
- Third paper specifically targeting his recurring weaknesses
- Question: "can he interact with this directly instead of you mediating?"
- Multi-subject Claude project setup designed
- Question: "how do I track progress?"
- Tracking system designed and re-designed several times
- Final shape: scheduled Cowork routine with calendar-aware planning, three differentiated emails, shared dossier

This trajectory matters because it explains why the system is overbuilt for what was originally asked and underbuilt for what it might eventually need to be. It evolved iteratively from real data, which makes it well-calibrated to {{STUDENT_NAME}} specifically — but also means the design choices weren't all made up front from a master plan.

## Key design decisions

These are decisions that future-you might want to revisit. Each is documented with what was chosen, what was considered, and why we landed where we did. If circumstances change, you'll know what to reconsider.

### Decision 1: Per-subject Claude projects, not one mega-project

**What was chosen**

Three separate Claude projects — Year 10 Maths, Year 10 English, Year 10 Science. Each with its own knowledge files (chapters, papers, rubrics) and its own project instructions tailored to the subject.

**Alternatives considered**

- One unified project with all subjects together
- One project per term that gets reset each term

**Why per-subject won**

Three reasons:

1. Project knowledge is shared across all chats inside a project. Maths textbook PDFs cluttering an English question's context is wasted attention.
2. Project instructions differ meaningfully by subject — a Maths instruction "refuse to give answers" works differently for English where "refuse to write essays" is a different rule.
3. Tutor visibility cleanly maps to one subject. The Maths tutor doesn't need to see {{STUDENT_NAME}}'s English work.

**When to revisit**

If {{STUDENT_NAME}} ever has 8+ subjects with active projects, the maintenance overhead of keeping 8 separate project knowledge folders current becomes annoying. At that point, consider grouping (humanities, sciences, languages) rather than per-subject.

### Decision 2: Socratic tutor stance, not answer machine

**What was chosen**

Project instructions explicitly tell Claude not to give answers when {{STUDENT_NAME}} asks. Instead, ask what he's tried, give the smallest possible hint, walk through one step only if needed. Refuse outright when he asks Claude to write essays or solve homework problems for submission.

**Alternatives considered**

- Helpful-by-default — Claude answers when asked, with caveats
- Strict refusal — Claude never gives answers, ever, even when {{STUDENT_NAME}} just wants to verify his own working

**Why Socratic won**

The single biggest risk of giving a 15-year-old unmediated AI access for school is that it becomes the path of least resistance. He hits a hard problem, asks Claude, gets the answer, moves on. Looks like progress. Isn't.

Socratic stance preserves struggle, which is where actual learning happens. Strict refusal would frustrate him into not using the system. The middle path — "ask what you tried, then hint, then walk through one step" — keeps him engaged and learning.

**Known weakness**

This works only if he uses the project. If he opens a fresh non-project Claude chat, none of these instructions apply. The defence is partly cultural (your conversations with him about what this is for), partly technical (his usage habits forming around the project). It's not bulletproof.

**When to revisit**

If he gets frustrated and stops using the project, soften the stance — fewer hints required before a step is shown. If he's clearly using it as a homework solver, tighten the refusal language and have a conversation.

### Decision 3: RAG over project knowledge, not general training

**What was chosen**

Project instructions specify that practice questions and explanations should draw from the textbook chapters and materials uploaded to project knowledge — not from Claude's general training. If a concept isn't in the project knowledge, Claude is told to say so rather than invent examples.

**Why this matters**

Practice has to align with what his school is actually teaching. Australian Year 10 NESA Stage 5 has specific notation conventions and methods that may differ from how Claude would solve a problem from general training. Wrong notation in his exam loses marks even when the answer is right.

**When this might fail**

If project knowledge is incomplete (chapters missing, syllabus not uploaded), Claude has nothing authoritative to ground itself in and may drift to general training silently. Per-term refresh of project knowledge is the mitigation.

### Decision 4: Both {{STUDENT_NAME}} and Dad receive feedback, but different versions

**What was chosen**

The Sunday routine sends three emails: full dossier with reasoning to Dad, short focused brief to the tutor (their subject only), motivating study plan to {{STUDENT_NAME}}. Same underlying data, three different framings.

**Alternatives considered**

- Single email to all three (cheaper, simpler) — rejected because each audience needs different content
- Just to Dad, who relays — rejected because it puts Dad back as the bottleneck the system was trying to remove
- Shared doc only, no emails — rejected because nobody opens shared docs unprompted; the email is the forcing function

**Why three emails won**

The audiences need genuinely different things:

- Dad needs the full picture and the reasoning behind recommendations, so he can sanity-check or override
- Tutor needs a focused 100-word brief telling them what to do in this week's session, nothing else
- {{STUDENT_NAME}} needs a motivating, forward-looking plan that doesn't read like an audit log

Forcing one email to serve all three would compromise each of them.

### Decision 5: Calendar for exam dates, not a separate document

**What was chosen**

Exam dates and scope live in a dedicated Google Calendar (`{{CALENDAR_NAME}}`). Title format: `[Subject] Exam: [Topic]`. Description includes scope, weighting, duration. The Sunday routine reads from this calendar each week.

**Alternatives considered**

- Separate exam scope document, hand-maintained — rejected for drift risk
- Both calendar AND document — rejected as worst of both worlds, two places to maintain
- No structured dates — let the routine ask {{STUDENT_NAME}} each week — rejected as unreliable

**Why calendar-only won**

The calendar is the source of truth for dates anyway (family planning, {{STUDENT_NAME}}'s own awareness). Putting scope in event descriptions means one place to update when school changes things. The connector for Google Calendar reads it directly each Sunday.

**Failure mode**

If Dad forgets to add an exam to the calendar, the routine doesn't know it's coming and recommends maintenance-level revision when intensity should be ramping up. Mitigation: school usually publishes term exam timetables 2-4 weeks in advance, so a periodic "check the school portal for new dates" habit is the real fix.

### Decision 6: 3-week calibration window before going live

**What was chosen**

For the first 3 weeks after launch, the routine emails Dad only. Dad reviews the output, manually forwards {{STUDENT_NAME}}'s and tutor's versions if they look right. After 3 weeks of stable, sensible output, the `CALIBRATION_MODE` flag in the routine is switched off and emails go to all three recipients directly.

**Why this matters**

The first run of any AI-generated weekly summary is unlikely to be perfect. Recommendations may be too aggressive or too conservative, tone may be off, format may need tightening. If {{STUDENT_NAME}} and the tutor see those rough versions, they may either dismiss the system or follow bad recommendations.

The calibration window is the safety net. Three weeks is conservative but appropriate — you need at least 2-3 weeks of real chat data accumulating before patterns emerge that the routine can recommend against.

**When to skip the calibration**

If you've already iterated heavily on the routine prompt before launch and you're confident it's producing what you want, you can skip calibration. Most people shouldn't.

### Decision 7: Scheduled Cowork routine, not Claude Code script or manual

**What was chosen**

The weekly dossier generation runs as a cloud-scheduled task in Cowork, every Sunday at 6pm. Cloud-scheduled means it runs on Anthropic infrastructure regardless of whether your laptop is on.

**Alternatives considered**

- Manual paste — {{STUDENT_NAME}} copies row data into a spreadsheet after each marking session. Rejected because it requires sustained discipline that's likely to drift.
- Claude Code cron job — local script you run weekly. Rejected because requires laptop on, requires you to remember, more maintenance overhead.
- Third-party MCP service like Composio for sheets integration — rejected because costs money and adds dependency on a third party for personal-use scale.
- Self-hosted MCP server — rejected because too much technical maintenance for one teenager's study system.

**Why Cowork won**

Cowork's cloud routines are first-party Anthropic infrastructure, run regardless of laptop state, and are part of the Max plan you already have. The setup is natural-language prompts saved as recurring tasks. No code, no deployment, no auth dance for the scheduling itself (only for the connectors the routine uses).

This decision was wrong twice during the design conversation — first I anchored on a Sheets connector that turned out not to be first-party Anthropic, then I corrected to Claude Code which Dad rightly questioned. Cowork was always the right answer; I just didn't know about it initially. The lesson: when designing systems involving recent Anthropic features, verify capabilities before committing to architecture.

**When to revisit**

If Cowork's cloud routines change significantly (pricing, limits, deprecation), or if the use case grows enough to justify a more robust pipeline, switch to Claude Code with proper CI.

### Decision 8: Forward-looking study planner, not just retrospective tracker

**What was chosen**

The Sunday email tells {{STUDENT_NAME}} what to revise next week — specific topics and effort levels — calibrated to days-to-exam. The dossier doesn't just show what happened; it tells him what to do.

**Alternatives considered**

- Pure tracking — score history and error patterns, no recommendations. Rejected because it puts the cognitive load of "what should I do this week?" on {{STUDENT_NAME}}, which is the bit he's least likely to do well.
- Aspirational — recommend ideal week regardless of current state. Rejected because would lose credibility fast.

**Why forward-looking won**

Dad's actual goal was "make him exam ready" — that requires guidance, not just reporting. A 15-year-old looking at a list of his weak spots may not connect that to "so do 3 papers on surds this week." The recommendation closes that gap.

**Risk**

Recommendations might be wrong, especially in the first weeks. If {{STUDENT_NAME}} follows a bad plan, the system has actively misled him. The 3-week calibration window is the primary mitigation. The reasoning section in Dad's email lets Dad sanity-check before recommendations go live.

### Decision 9: Three practice papers as the foundation, not just exam prep

**What was chosen**

Three full practice papers were built during the design conversation:

- Paper 1 — standard difficulty, Chapters 4.03-4.16 (46 marks, 45 min)
- Paper 2 — harder, more questions, traps. (~58 marks)
- Paper 3 — targeted at specific weaknesses observed across Papers 1 & 2 (60 marks)

**Why this matters**

These weren't just exam preparation — they generated the actual data the system runs on. Each marked paper revealed patterns. Paper 3 was designed specifically to challenge weaknesses found in Papers 1 & 2. The whole tracking and dossier system has real signal because {{STUDENT_NAME}} has done real practice.

If you're starting a new subject (say Geography in Term 2), don't skip the equivalent step. Generate at least 1-2 practice papers and have him do them before the dossier system has anything to track.

### Decision 10: Push-via-Gmail, not pull-from-projects (architecture pivot, May 2026)

**What was chosen**

Per-session data flows from Claude projects to Gmail, where the Sunday routine reads it. NOT routine-pulls-from-project-chats.

Concretely:
1. **Capture:** at the end of every marking / feedback / serious practice session, each subject's project instructions tell Claude to **draft a Gmail to Dad** with subject `[SESSION_SUMMARY] <Subject> <YYYY-MM-DD>` and the structured `[SESSION_SUMMARY]` block as body. Dad reviews and clicks Send (≈10 seconds).
2. **Aggregate:** the Sunday Cowork routine searches Gmail for that subject prefix `newer_than:7d`, parses the blocks, joins with calendar data.
3. **Persist:** the routine creates **a new dated Google Doc per week** (`<Student> — Week of YYYY-MM-DD`) inside a shared Drive folder. The folder itself is the chronological archive. The original "rolling dossier" doc becomes a static landing page for manual content (interventions log, parent's notes), never overwritten by the routine.
4. **Deliver:** the Sunday routine produces **Gmail drafts** (not direct sends). Dad clicks Send for each. Drafts pile up visibly if a week is forgotten.

**Why this changed**

The original Decision 7 architecture assumed Cowork routines could (a) read Claude project chats and (b) write to existing Google Docs. The first run of the routine surfaced both as false:
- Cowork tasks have no API surface for reading Claude project conversations on the same account.
- The Google Drive connector exposes content reads (`read_file_content`, `search_files`) and file creation (`create_file`) but no `update_file_content`.

This is the third time this design has hit the same root cause — assuming a recent Anthropic capability without verifying it ([decisions 7](#decision-7-scheduled-cowork-routine-not-claude-code-script-or-manual) and [the second mistake catalogued in "Things we got wrong"](#i-anchored-on-the-wrong-tracking-architecture-twice)). At that point, the right move is to redesign around verified primitives, not to wait for a feature.

**Why push-via-Gmail wins**

- **Verified primitives only.** Gmail draft creation, Gmail search, Drive folder + new-file creation, Calendar read — all confirmed working in this account/connector setup.
- **Auditable.** Every datapoint the routine sees passed through Dad's Sent folder. There are no rogue summaries fabricated from chat history.
- **Failure mode is benign.** If {{STUDENT_NAME}} doesn't use the projects, no `[SESSION_SUMMARY]` drafts get created → the routine reports zero activity, correctly. If Dad forgets to click Send, drafts pile up in Gmail visibly — recoverable.
- **Decouples from Anthropic platform changes.** Whether or not Cowork ever exposes project-chat search, this design works.
- **Same intent, fewer load-bearing assumptions.** The Socratic stance, RAG grounding, three-recipient differentiation, calibration window, and scope discipline all survive unchanged.

**Honest costs**

- Dad becomes a human ACK on every session-summary email (~10 sec per session). Originally the system promised zero per-session friction; now it promises ~10 seconds.
- The dossier is no longer a single rolling doc. Each week is its own doc inside a folder. Nice side-effect: the archive is naturally sortable and immune to accidental overwrite.
- Two soft dependencies in the data path ({{STUDENT_NAME}} using projects + Dad sending drafts). The first was always true; the second is new but visible.

**When to revisit**

- If Anthropic exposes a project-chat read API to Cowork routines, the Capture step could revert to pull. But by then this design will be the boring stable thing and switching back would be churn for marginal gain.
- If the Drive connector gains an `update_file_content` tool, the per-week-doc model can collapse back to a rolling dossier. Same reasoning — don't switch unless the pain is real.

## Things we got wrong, or partly wrong

Honesty section. These are places where the design conversation drifted, was wrong, or required correction.

### I anchored on project-chat introspection that doesn't exist (the third miss)

The original architecture (Decisions 7 and earlier) treated "Cowork routine reads three Claude projects on the same account" as a primitive. It isn't. There's no exposed surface for that today, and the first real run of the routine made it obvious — the routine simply could not see {{STUDENT_NAME}}'s chats.

Worse: the same root cause as the first two misses. I assumed a recent platform feature existed, didn't verify it, and only found out when the system tried to run for real. The lesson from Decision 7 ("verify Anthropic feature availability before committing to architecture") was *literally written down* and I still missed this case.

The fix was to flip the data flow — Decision 10. Projects push session summaries via Gmail draft instead of being pulled from. This sidesteps the missing primitive entirely and uses only verified tools (Gmail search/draft, Drive folder + new-file creation, Calendar read).

Lesson: when "verify the primitive" is a written rule, treat the *whole input layer* as suspect, not just the obvious bits. I verified Cowork scheduling existed (yes) but not Cowork's read access to project chats (no). Both are needed for the original design to work; checking only one was the mistake.

### I anchored on the wrong tracking architecture twice

First: I recommended a Google Sheets connector inside Claude that I assumed was first-party Anthropic functionality. It wasn't — it required either a paid third-party service or a self-hosted MCP server. Dad caught this and pushed back. I corrected to Claude Code as the alternative.

Then: Dad asked about Cowork scheduling. I didn't know Cowork had scheduling. Searched the web, found out it does, and that it was the right answer all along.

Two iterations of architecture were wasted before we landed on the right answer. Lesson: when designing around recent Anthropic features (Cowork, connectors, routines), verify capabilities up front rather than assume from training data.

### I overbuilt before validating

During the tracking design, I produced an example dossier with five sections and detailed structure before we'd run the system once. The example was useful as a concrete artifact for Dad to react to, but it also locked in a structure that may not survive contact with reality. The first 3-4 weeks of real data will reveal which sections get used, which get ignored, and which are missing.

Lesson for future iteration: don't add sections to the dossier just because they sound useful. Add them when real Sunday emails reveal a gap.

### I kept pushing simpler, sometimes too hard

Throughout the conversation, I repeatedly recommended scaled-back versions — manual paste, no automation, simpler architectures — when Dad was asking for more sophisticated builds. Some of this was right (premature optimisation is real, behavioural drift kills automation) but some was over-applied. Dad correctly noted he had Max and Cowork and could absorb the complexity.

Lesson: when the user has stated their goal clearly and has the resources to execute, don't keep proposing the simpler version unless there's specific evidence the more sophisticated version will fail.

### I forgot Dad's preferences repeatedly

Dad's stated preferences include "never use em dashes" and "be as concise as possible." I used em dashes throughout the conversation and produced lengthy responses anyway. This isn't a design flaw in the system but it is a persistent personal failure of mine that's worth noting if future-Claude reads this — pay attention to user preferences across the conversation, not just at the start.

## How the system is likely to evolve

Predictions about what changes you'll likely make in the next 12 months. Use as a planning input, not a guarantee.

### In the first 4 weeks

- The routine prompt will need 2-3 tweaks based on actual output (tone, recommendation aggressiveness, email length)
- You'll discover that one subject has more structured data than another (probably Maths) and the dossier format may diverge per subject
- {{STUDENT_NAME}} will have feelings about the system. Some he'll voice, some he won't. Listen for both.

### By end of Term 1

- You'll know whether the system is genuinely useful or theatre. Tell-tale: are you and the tutor making different choices because of it?
- Some sections of the dossier will be unused. Trim them.
- New patterns will emerge that you'll want tracked (e.g. "time per question" rather than just "score")

### By end of Term 2

- You'll have added or removed subjects. Geography or Legal Studies might join. A subject {{STUDENT_NAME}} drops will need to be archived.
- The exam calendar will have more events and the routine's prioritisation logic may need tightening when 3 exams cluster in 10 days
- You'll have at least one Sunday where the routine produces obviously wrong output. The repo's commit history will help you debug it.

### By Year 11

- {{STUDENT_NAME}} himself will likely be the primary maintainer of his projects. Hand over progressively.
- The Socratic stance may need to relax — senior students legitimately need more direct help, not less.
- The system will probably need rebuilding for the senior curriculum, not just extending. Year 11/12 has different texture (depth, ATAR-stakes, university-prep).

## Early signal (Term 2, 2026)

One data point worth recording, with appropriate caveats.

{{STUDENT_NAME}}'s Term 2 Maths assessment improved from A to A+. He
finished the paper with seven minutes to spare and reported feeling
confident going in. This was after roughly one term of partial use of
the system — the per-subject project with Socratic tutoring was active
throughout, the v3 Sunday routine had been calibrating for the latter
weeks of the term.

**What this is not:**

- Evidence the system works. One assessment in one subject after one
  partial term is a single data point. Replication across subjects and
  terms is required before the system can be said to have done anything.
- Attributable cleanly to this system. Confounds include: natural
  maturation, better tutor-student fit in Term 2, easier or different
  topic coverage, regression to the mean from a strong Term 1
  performance, and the Hawthorne effect from his awareness that the
  system was watching.
- A replicable claim. English and Science have not produced equivalent
  signal yet. The Maths result may not generalise.

**What this might be:**

- The first signal the Socratic stance worked. The confidence
  self-report is the most telling element. It suggests he went in
  feeling like he understood the material, not like he had memorised
  someone else's answers. That is the design intent of the Socratic
  stance from Decision 2.
- Validation of the calibration window (Decision 6). The system did
  not interrupt his Term 1 routine with half-formed recommendations.
  By the time it was generating recommendations he could act on, the
  patterns were grounded in real data.

**What to watch in Term 3 and beyond:**

- Does the result replicate in Maths, or was Term 2 a one-off?
- Does English or Science show similar improvement, or is the system's
  utility subject-specific?
- Does his confidence going into assessments hold up across multiple
  exams, or was Term 2 emotionally favourable for unrelated reasons?
- If the system is dropped (e.g., for a term as a natural experiment),
  do results regress? This is the cleanest test but also the highest
  cost to run.

Recording this here so the claim is on the record with the caveats
intact, rather than as a headline somewhere later. If the system is
ever cited publicly as effective, the citation should come back to
this entry and the subsequent terms' data, not to Term 2 in isolation.

## If something breaks, read this

Six months from now, something will have broken. Likely candidates and what to do:

### The Sunday routine produces empty or thin output

Most likely cause: {{STUDENT_NAME}} hasn't been using the projects much that week. The routine reads what's there; it can't fabricate engagement.

Less likely cause: the project chats happened in a fresh non-project Claude window so the routine can't see them.

Don't fix this with tooling. Have a conversation.

### The recommendations are wrong

Most likely cause: exam calendar is missing a date or has wrong scope, so the routine is calibrating to the wrong timeline.

Second most likely: the project knowledge for that subject hasn't been refreshed for this term and Claude is recommending revision of last term's topics.

Fix the data inputs, not the routine prompt.

### The email doesn't arrive

OAuth token for Gmail expired. Re-authorise in Claude settings.

Or: routine errored. Check Cowork's task history.

Or: it's in your spam folder.

### {{STUDENT_NAME}} complains the email feels like nagging

This is a tone issue, not a system issue. Edit the routine prompt's section on {{STUDENT_NAME}}'s email — soften the tone, lead more with wins, drop any "need to do more" framing.

If the underlying issue is that he genuinely is behind and any honest email will feel like nagging, that's a different conversation.

### The tutor stops engaging with the briefings

Probably means the briefings aren't useful in their current form. Ask the tutor what would actually help. Adjust the prompt.

Or: tutor has changed and the new tutor needs onboarding to the system. Send them the dossier doc and explain how to read the email.

### You stop reading the Sunday email

This is the most important failure mode. If you're skimming and not acting, the system has bought you nothing.

Ask: is the email itself the problem (too long, wrong content), or is the practice underneath it the problem ({{STUDENT_NAME}} not engaging, recommendations not actionable)?

If it's the email, edit the routine prompt to surface less and prioritise more.

If it's the practice, the tracking system is downstream of the actual problem. Have the conversation.

## Note for future Claude (or any AI maintainer)

If you're an AI being asked to modify this system in the future:

- This was built for a specific 15-16 year old in Australian Year 10 in 2026. His name is {{STUDENT_FULL_NAME}}. He attends {{SCHOOL_NAME}}. He's a strong student with specific recurring weaknesses (distribution into brackets, perfect-square middle terms, reading discipline on instruction words). The system encodes specifics about him.
- The Socratic stance and refusal-to-write-essays parts are non-negotiable design choices. Don't soften them without understanding why they exist.
- Dad (the user who commissioned this) is technically capable and wants to enable {{STUDENT_NAME}}, not control him. The shared dossier is for visibility, not surveillance.
- Tutor relationships are real, third-party. Don't make changes that affect what tutors see without checking with Dad first.
- Verify Anthropic feature availability before committing to architecture. I got this wrong twice in the original design conversation. The available capabilities change month to month.
- If you're modifying the routine prompt: the prompt is long but every section earns its place. Read the whole thing before editing one part. Many design choices have second-order consequences.
- The repo is the source of truth. Cowork is the deployment. If you make a change in Cowork without committing to the repo, that change will be lost the next time someone re-deploys from the repo.

End of design history. Save this in the repo as `docs/design-history.md`. Update it when major design decisions are revisited. Don't delete entries — append revisions instead, so the history stays intact.
