# 04 · X-Ray Vision — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly. Last session the numbers showed you what happened: a
ping used to wait ninety seconds and now it waits sixty, people
missed pings they used to catch, and missing one counts the same as
turning one down — so four responders stopped hearing from us
altogether.

At the end of the session, type wrap up, and Claude Code saves the
prompts you wrote yourself below — not the starter prompt — updates
CLAUDE.md, and saves your work to GitHub. By Module 6 this file is a
prompt library built from your own questions.

---

### 1.

What changed in this routing flow in Release 4.2? Show me the exact files and logic that changed, then explain the impact in plain English.

### 2.

What in this code could cause a responder who used to receive pings to start receiving fewer pings or no pings at all? Walk me through every possible path.

### 3.

Now tell me what this routing logic does NOT account for. Are there any situations where a responder could fall out of the normal flow and have no obvious way back in?

### 4.

The pings table on rook-database has last session's numbers. Read that alongside this code. Does the data back up the hypothesis I found in Lab A? Show me the evidence for and against it. And does that answer Marcus's question about whether the change to who gets asked first affected responders who had already been turning jobs down, or only new responders?

### 5.

Find responders who were already declining or missing pings before August 12. What happened to how often they were pinged after Release 4.2? Show me the rows behind your answer.

### 6.

Compare responders with a strong response history before August 12 against responders who had already been declining or missing pings. Did Release 4.2 affect those groups differently?

### 7.

Try to disprove my Lab A hypothesis using the pings data. Is there anything in the actual responder behavior that contradicts what the code made us expect?

### 8.

Find me the part of this code that takes points off somebody when they miss a ping or turn one down. Show it to me and explain it in plain English. Then find me every single thing in this code that puts points back on.

### 9.

Somebody has been quiet for a month. Walk me through, step by step, exactly what would have to happen for them to start getting work again.

### 10.

Is there any way for someone to recover their score without first receiving and accepting another ping? Search the whole routing code before answering.

### 11.

Is there any way for someone to recover their score without first receiving and accepting another ping? Search the whole routing code before answering.

### 12.

Could someone reach a point where their score is too low to receive pings, but receiving and accepting a ping is the only way to improve their score? If yes, show me exactly how that happens in the code, step by step.
