# LinkedIn post — parent-facing draft

Target audience: parents thinking about whether AI can play a useful
role in their kids' study. Hook is sized to fit under LinkedIn's 156-
character first-line truncation.

---

I built an AI study companion for my Year 10 son. The first two designs didn't work. The third one does, and the difference is the point of this post.

The goal was simple. He has three subjects, three tutors, exam dates spread across a term, and a parent (me) trying to keep an honest picture of his progress without becoming the bottleneck. The plan: per-subject Claude projects with Socratic tutoring, a Sunday routine that emails me, my son, and each tutor with a tailored briefing.

Architecture v1 assumed a Sunday routine could read his project chats and update a rolling Google Doc. Looked clean on paper. The first real run showed neither was actually possible with the tools I had. Cowork routines do not have an API surface for project chats. The Drive connector is read-only for content. Two load-bearing assumptions, both wrong.

So I redesigned. v2 flips both arrows. Each subject project now drafts a Gmail to me at the end of every marking session. I review the draft and click Send. The Sunday routine searches Gmail by subject prefix instead of reading project chats. It creates a new dated doc each week instead of updating a rolling one. Same intent, different primitives, verified working.

What I would tell other parents thinking about doing this:

1. Start with a real practice paper, not an architecture diagram. Generate one, mark it, see what you learn. The system shape reveals itself.

2. The Socratic stance is non-negotiable. If the project hands him answers, it becomes the path of least resistance. Refuse to write essays. Refuse to solve homework problems for submission. Hint, then one step, then stop.

3. Expect the first design to break against reality. Mine did. The "things we got wrong" section in the design notes is now the most useful page in the repo.

4. The system only works if he uses it. No tooling decision can fix that.

Open-source repo with the redacted spec, full design history, the failed v1, and the working v2:
https://github.com/rajmurugan01/study-loop

If you're a parent thinking about something similar and want to talk it through, comment or DM. Happy to share what worked and what did not.

---

**Stats:** ~2,000 characters, 9 paragraphs. Hook is 156 characters
(sits under the "see more" truncation).

**When you post:**

- Drop in a screenshot if you have one (redacted dossier doc title, or
  the Mermaid diagram from the repo README rendered).
- LinkedIn previews of github.com URLs are reasonable but not great.
  Consider posting the URL on its own line at the bottom so the link
  card renders cleanly.
- Best posting windows for engagement: Tue–Thu, 7–9 AM local time, or
  Sun evening. Avoid Fri afternoon and weekend mornings.
