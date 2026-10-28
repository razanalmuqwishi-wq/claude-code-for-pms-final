# 03 · Rewind — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly.

Last session you read four conversations and every support ticket
since 4.2 — and found the two piles did not agree.

At the end of the session, type wrap up, and Claude Code saves the
prompts you wrote yourself below — not the starter prompt — updates
CLAUDE.md, and saves your work to GitHub. By Module 6 this file is a
prompt library built from your own questions.

---

### 1.

Which responder behavior changed the most after August 12: accepted, declined, or missed pings? Show me the before-and-after numbers and the rows supporting the conclusion.

### 2.

Was the change after August 12 immediate, or did it happen gradually over the following weeks? Break it down week by week and show me the underlying rows.

### 3.

Could the change after Release 4.2 be explained by a change in the number of pings being sent rather than responder behavior? Compare ping volume and response outcomes before and after August 12.

### 4.

Compare the support_tickets table on rook-database against the pings table. Do they tell the same story about what changed around Release 4.2? Show me the relevant weekly numbers from both sources and tell me when each one actually starts to move.

### 5.

Does the change in support tickets happen before, at the same time as, or after the change in responder behavior in pings? Show me the timeline week by week.

### 6.

What is the strongest evidence that the support ticket trend and the responder behavior trend are related, and what evidence suggests they might not be? Use specific numbers from both tables.

### 7.

Take the one number I chose to describe the impact of Release 4.2 and try to disprove it using the support_tickets table. Does my original conclusion still hold? If not, what should I say instead?

### 8.

From the pings table, identify one responder who clearly went quiet during this period and would be useful to investigate. Tell me their name and briefly explain why you picked them.

### 9.

Follow this responder from June onward, week by week. For every week, show how many times we pinged them and what they did with each ping: accepted, declined, or missed. Use their name and explain the pattern in plain English.

### 10.

When exactly did this responder's behavior start to change? Identify the first week where their pattern was meaningfully different from their earlier behavior and show me the evidence.

### 11.

Compare this responder's pattern with the overall responder population. Is their experience typical of what happened after August 12, or are they an outlier? Show me the numbers behind your answer.
