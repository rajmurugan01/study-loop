Artifact 4: Setup instructions
Step-by-step build. Estimated 90-120 minutes total. Do in one session if you can; the steps build on each other.
Phase 1: Connectors and accounts (~15 min)
1.1 Verify your Max plan and Cowork access
Open claude.ai in browser, log in
Go to Settings → Plan & Billing
Confirm Max plan is active
Open Claude Desktop app (download from claude.com/download if not installed)
Look for the Cowork tab in the desktop app — confirm it's there
1.2 Connect Gmail, Google Docs, Google Calendar
In Claude (web or desktop), go to Settings → Connectors
Add Gmail connector — follow OAuth flow, grant access to send email
Add Google Docs connector — OAuth, grant read/write access
Add Google Calendar connector — OAuth, grant read access
Test each by asking Claude (in any chat): "can you list my Google Calendars?" — if it returns a list, calendar is connected
If a connector fails or isn't available in your Claude settings, tell me which one — there are workarounds for each but they vary.
Phase 2: Calendar and Doc setup (~15 min)
2.1 Create the exam calendar
Open Google Calendar in browser
Create a new calendar called exactly: "{{CALENDAR_NAME}}"
For each exam you have dates for, create an all-day event
Event title format: "[Subject] Exam: [Topic name]" (e.g. "Maths Exam: Term 1 — Products, Factors and Surds")
Event description: include scope (chapters/topics), weighting (% of mark), duration (45 min, 90 min, etc.)
Save
For exam dates you don't have yet, create placeholder events with the title "[Subject] Exam: TBC" and an estimated date based on the school's term calendar. The routine will note these as estimates. Update once you have real dates.
2.2 Create the shared Google Doc
Open Google Drive
Create a new Google Doc titled "{{DOSSIER_DOC_NAME}}"
Open Artifact 2 (the dossier template) and copy the structure into the Doc
Click Share — add {{STUDENT_NAME}}'s Gmail with View access
Click Share again — add tutor's Gmail with View access
Confirm tutor and {{STUDENT_NAME}} don't have edit rights
Copy the Doc URL — you'll need it for the routine prompt
Phase 3: Three Claude projects (~30 min)
3.1 Maths project
In Claude, click + to create a new project. Name it "Year 10 Maths"
Open Project Settings → Project Instructions
Paste the Maths project instructions from the previous setup pack (Artifact 1 of that earlier pack)
Open Project Knowledge → Add files
Upload: textbook chapters (current term), Practice Papers 1-3 + answer keys, {{STUDENT_NAME}}'s marked papers, NESA Stage 5 Maths syllabus
Done
3.2 English project
Create new project: "Year 10 English"
Paste English project instructions
Upload: texts being studied, teacher's rubric, past essays with feedback, syllabus
3.3 Science project
Create new project: "Year 10 Science"
Paste Science project instructions
Upload: textbook chapters, past practical reports, syllabus, marking criteria
If you don't yet have all the materials (rubrics from teachers, past papers from school), upload what you have. The system works incrementally — you can add files later and Claude will pick them up automatically.
Phase 4: The routine itself (~30 min)
4.1 Test as a one-off task first
Open Cowork in Claude Desktop
Click + New Task
Open Artifact 1 (the routine prompt)
Edit the placeholders ([YOUR_EMAIL], [DOSSIER_DOC_URL], etc.) to your real values
Set CALIBRATION_MODE to true (so emails go to you only)
Paste the edited prompt into Cowork
Click "Let's go" — let it run as a one-off
Watch what it does. Read the email it sends. Open the Doc and see what it wrote.
4.2 Iterate
First run almost certainly won't be perfect. Likely issues:
Empty or thin sections because there's not much chat history yet — normal, will improve as {{STUDENT_NAME}} uses the projects
Tone slightly off — note what to adjust
Email format awkward — note specifics
Plan section over-recommending — note this and tighten the prompt
Edit the prompt, run again as one-off. Repeat until you're happy.
4.3 Save as scheduled routine
Open the task in Cowork
Type /schedule in the chat input
Choose Weekly, Sunday, 18:00 (6pm)
Choose "Cloud routine" (so it runs even if your laptop is off)
Save
Phase 5: Calibration window (3 weeks)
With CALIBRATION_MODE on, each Sunday:
Routine runs at 6pm
You receive the dossier email by 6:15pm
You read it. If recommendations are sensible, forward {{STUDENT_NAME}}'s version (which is in the email) to him manually. Forward tutor's version to tutor.
If recommendations are off, edit the routine prompt for next week
After 3 weeks of stable, sensible output, switch CALIBRATION_MODE to false
Phase 6: Live mode
Edit the routine prompt: change CALIBRATION_MODE: true to false
Save
Tell {{STUDENT_NAME}}: "From this Sunday you'll get a weekly study plan email from Claude. It's calibrated to your real progress and exam dates. The dossier in Drive has more detail if you want it. The tutor gets a brief version too."
Tell tutor (or have {{STUDENT_NAME}} tell them): "You'll get a weekly email Sunday evening with the focus for our session. Doc with full context: [link]"
Phase 7: Maintenance
Per term:
Replace last term's chapters in each project knowledge with this term's
Update the exam calendar with new dates
Review the routine prompt — does it still match how things actually work? Tune if needed
Per week:
Read the Sunday email. 5 minutes.
Open Doc once if something in the email warrants more depth. 5-10 minutes.
Talk to {{STUDENT_NAME}} about anything specific the data surfaced. As long as it takes.
Per month:
Skim the project chats themselves to make sure {{STUDENT_NAME}} isn't using Claude to bypass real practice
Check OAuth tokens are still valid (if a connector silently fails, the routine breaks)
Phase 8: Failure modes and how to handle them
Routine doesn't run on Sunday
Check claude.ai/code/routines or Cowork's Scheduled tab — was the run skipped, errored, or just delayed?
Most common cause: OAuth token expired. Re-authorise in Settings → Connectors.
If unclear, run the routine manually as a one-off.
Routine runs but produces wrong output
Check the routine log section in the Doc — Claude logs each run with notes.
Edit the prompt to address what went wrong.
Run as one-off to test the fix before next Sunday.
Email doesn't arrive
Check Gmail spam folder.
Check the email address spelling in the prompt.
Verify Gmail connector is still authorised.
{{STUDENT_NAME}} stops engaging
Routine surfaces this — "no activity for X days" notes in the email.
Don't try to fix this with tooling. Have a conversation with him.