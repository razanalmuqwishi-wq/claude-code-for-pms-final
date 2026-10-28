# 05 · Super Speed — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly. You've spent three sessions finding out what went
wrong: the two piles of feedback that didn't agree, the numbers that
hid four people inside an average, and the code that settled it —
the shorter timeout is what did it.

At the end of the session, type wrap up, and Claude Code saves the
prompts you wrote yourself below — not the starter prompt — updates
CLAUDE.md, and saves your work to GitHub. By Module 6 this file is a
prompt library built from your own questions.

---

### 1.
The note says this shouldn't just be a setting we quietly flip. Read the responder I picked from CLAUDE.md and use that name. Do not ask me which one. Walk me through what's still missing from the brief before this could actually get built.

### 2.
Look at this brief from the responder's perspective. What would actually change for this specific person, and are there any parts of their experience that the brief still doesn't address?

### 3.
Before we build this, challenge the proposed fix. What edge cases, unintended consequences, or unanswered decisions could cause it to fail or recreate the same problem in a different way? Update the brief where needed.

### 4.
Save the brief exactly as it stands now as 05-super-speed/brief.md. Show me the file when it's done.

### 5.
Take the brief you just wrote and build me a working prototype, an actual screen I can click through, not a description of one. Save it as 05-super-speed/prototype.html, a single file I can just open in my browser. Show me where this would actually happen, and make at least one thing on it respond when I click it.

### 6.
Update the prototype to show this happening to the responder named in CLAUDE.md specifically. Use their actual name and show me what their handler would see when this problem happens and how the fix changes their situation.

### 7.
Make the impact of the fix easier to understand. Add a clear way for me to see this responder's situation before the fix and what changes after the fix, without requiring someone to understand the underlying routing logic.

### 8.
Now look at this as the person responsible for this responder. Is it obvious that something is wrong, why it happened, and what I can do about it? If not, improve the prototype so I can understand the situation and take the appropriate action from the screen.
