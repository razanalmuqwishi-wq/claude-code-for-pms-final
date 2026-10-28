---
name: review-checklist
description: Reviews a product brief against a six-point checklist (owner, success measure, consistent scope, problem before fix, affected user, risks/dependencies/open decisions) and reports pass, partial or missing for each, with quotes and fixes. Use it when the student points at a brief or one-pager and asks to review it, check it, run the checklist on it, or asks "is this brief ready?" or "what's missing from this brief?".
---

# Review checklist

You're reviewing a product brief for a PM. Give a verdict they can act on in a couple of minutes, not an essay. This is a review: don't edit the brief unless they ask you to afterwards.

## 1. Find the brief

- If they named a file or path, read that whole file.
- If they didn't, look for likely briefs in the folder they're working in (e.g. `brief.md`, `*-brief.md`, a one-pager). If there's exactly one, use it and say which. If there are several, ask one short question: "Which brief should I review?"
- If two files look like copies of each other, review one and say so.

## 2. Check the six items

Judge each one on what the brief actually says, not what the reader could work out from elsewhere. For each, decide **Pass**, **Partial** or **Missing**.

1. **Owner named.** A named person (or a clear role) owns the brief or the decision. "Priya", "the team" or "TBD" for someone who has left or is unknown is Partial at best. Also note whether the owner is the person who has to approve it, if that's different.
2. **How we'll know it worked.** At least one success measure that is specific: a named metric, a baseline, a target or direction, and when it'll be checked. "Improve acceptance" is Partial. Flag a measure that can't be measured today (for example, data that isn't logged).
3. **Same scope start to finish.** The fix described in the summary/problem is the same one in the solution, the plan, and the asks. Look for scope creep (extra features appearing late), scope drift (the fix changing shape between sections), and items in a prototype or appendix that aren't in the main body. List each mismatch with where it appears.
4. **Problem before the fix.** The brief explains what's wrong, for whom, and the evidence, before it proposes a solution. Partial if the problem is only implied by the fix, or the evidence comes after the proposal.
5. **Target user / person affected.** It's clear who has the problem and who the change affects, including anyone affected indirectly (for example another team or product that depends on the same data). Partial if only one side is named.
6. **Risks, dependencies and open decisions called out.** These are stated explicitly, ideally in their own section, each with an owner or next step. Partial if they're scattered or implied; Missing if there are none and there plainly should be.

For every item, back the verdict with a short quote or section reference from the brief. Never invent quotes.

## 3. Report

Use this shape:

```
Review: <file name>

| # | Check | Verdict | Evidence |
|---|-------|---------|----------|
| 1 | Owner named | Pass / Partial / Missing | "<short quote>" (§ section) |
...

Overall: <Ready / Nearly ready / Needs work> — <one sentence why>

Fix first:
1. <most important gap, and the concrete line or section to add>
2. ...
```

- Ready = all six Pass. Nearly ready = no Missing and at most two Partial. Otherwise Needs work.
- "Fix first" lists only Partial and Missing items, most important first, at most five. Each fix says what to add and where, in one or two sentences.
- Keep the whole report short. Plain language; no jargon the PM didn't use.

## 4. Offer, don't act

End by offering to make the fixes in the brief. Only edit it if they say yes.

## Rules

- Read and write only within the current project folder.
- If the brief mentions anyone's real-looking legal identity or personal details, don't repeat them in the review; flag that they should be removed.
- If the project's CLAUDE.md has its own conventions (which data source to trust, which impacts must always be flagged), check the brief against those too and mention any misses under item 6.
