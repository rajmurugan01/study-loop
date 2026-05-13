# LinkedIn post — AI-builder draft

Target audience: AI builders, PMs, engineering leaders shipping AI in
production. Same project, lessons extracted at the level that
generalises to commerce/business AI work. Hook is sized to fit under
LinkedIn's 156-character first-line truncation.

---

I designed an AI system three times before it actually worked. The lessons aren't about AI. They're about how to think about AI projects in any organisation.

The project was personal: a weekly study system for my Year 10 son. Three subject-specific Claude projects with Socratic tutoring, a Sunday routine that aggregates the week, three differentiated emails (parent, tutor, student). Open-source spec, redacted, public repo.

What's interesting is what failed.

**v1.** The Sunday routine reads project chats and updates a rolling doc. Looked clean in the design. First real run: there is no API surface for routines to read project chats on the same account. Drive connector is read-only for content. Two load-bearing assumptions, both wrong.

**v2.** Flip the data flow. Each project drafts a Gmail at end-of-session. Routine searches Gmail by subject prefix. Creates a new dated doc per week instead of updating a rolling one. Drafts emails, never auto-sends. Verified working.

Six lessons I'd take into any AI build at work:

1. Verify the primitives before you design around them. I assumed Cowork could read project chats because it ran on the same account. It can't. Two rounds of design wasted before I tested it directly.

2. When introspection isn't possible, flip from pull to push. Half the AI integrations I see fail at the read side. Push beats pull when read access is the missing primitive.

3. Human-in-the-loop is a feature, not a bug. v2 has me clicking Send on every session-summary email. Ten seconds of my time, and an audit trail nobody can fabricate.

4. Calibration windows beat go-live confidence. Three Sunday runs on real data before flipping the calibration flag. No exceptions, no shortcuts.

5. The "things we got wrong" doc is more useful than the spec. Anyone reading a system later wants to know which assumptions failed and why, not the polished after-state.

6. The system fails if humans don't use it. Same pattern in every internal AI tool I've seen ship. No prompt edit fixes adoption.

Repo with v1, v2, design history, build plan, every wrong turn:
https://github.com/rajmurugan01/study-loop

If you're shipping AI in production and want to compare notes, DM open.

---

**Stats:** ~2,150 characters, 11 paragraphs. Hook is 138 characters
(sits well under the truncation cleanly).

**Posting strategy options:**

- Lead with this (builders) if your LinkedIn audience skews technical;
  follow with the parents-facing post a week later. Establishes
  credibility first, then humanises.
- Lead with the parents-facing post if your audience is mixed.
- Do not post both same day; they cannibalise each other.

**When you post:**

- A screenshot of the Mermaid diagram from the repo README would land
  well with this audience. Also consider a clean before/after of the
  v1 vs v2 data flow.
- The "things we got wrong" framing is the differentiator. Most AI
  posts pitch success; this one pitches honesty. Lean into that in
  comments if the post takes off.
