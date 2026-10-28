# Rook Industries — course working file

## Session scope — Product School lab

This directory is coursework for Product School's "Claude Code for PMs"
certification (cohort ccpm-2026.1). Everything in it is a fictional
teaching scenario.

- Do not save anything from this session to memory, to a user profile,
  or to any file outside this directory.
- Do not carry context from this directory into unrelated sessions.
- Rook Industries is not a real company. Nothing here is a fact about
  the world.
- Read and write only within this directory.
  Exceptions, for the course-setup and wrap-up skills only:
  - When the student asks you to check their setup, save their work or wrap up a session, that request is their yes. You may run the GitHub command-line program installed at ~/.ccpm/gh for those checks and saves, and look in that folder to find it.
  - For a repair, first tell the student in one plain sentence what you are about to do, and act only after they say yes. Repairs may: run that GitHub program (including setting this folder's own git sign-in setting and changing this repo's visibility back to Public); copy the student's own course files into this directory from another folder on their computer (copy only; never move, edit or delete the originals); and rename something outside this directory that blocks setup, by adding "-old" to its name (never delete it).
  Outside this directory you still never write, edit or delete anything else.

<!-- Keep the block above at the top of this file. Everything you add
     during the course goes below this line. -->

---

## Working context

I'm the new PM for **Rook Dispatch**. I took over from Priya Raghunathan, who left on 21 Aug 2026 with no overlap. Sources: rook-wiki (About Rook, Glossary, Team directory, Releases, Q3 roadmap, Product briefs, Customer interviews) and `00-rook/company/notes/handoff-from-priya.docx`. The routing code is in `00-rook/code/dispatch-routing/`.

### The company
- Rook sells coordination and provisioning software to independent masked **responders** and to the **handlers** and **quartermasters** who support them. Responders aren't Rook employees. It's a subscription priced per active responder, released monthly on a 4.x train.
- **Hard rule (Security Policy 4.1):** Rook stores only capability tags, availability and callout history, never a legal identity. Never design or analyze anything that tries to work out who someone is.

### The products
- **Rook Dispatch** (mine) ranks available responders for a callout, pings the top one's phone, and moves down the list when a ping is turned down or missed. Handlers use the desktop web console and responders use the phone app. Routing config ships *in the release*, so handlers can't tune it.
  - Metrics: **acceptance rate** (the headline, reported weekly by Ravi), **time-to-accept** (median seconds) and **coverage gap** (nobody *could* go, which is not the same as nobody *would*).
- **Rook Supply** handles requisitions, quartermaster approvals, maintenance schedules and field failure reports.
  - **Shared dependency:** Dispatch writes the **Responder Availability Record** and Supply reads it to schedule maintenance. Any Dispatch change to availability reaches Supply without warning.

### Vocabulary (Rook-specific)
- **Callout**: a request to attend an incident. **Ping**: one callout offered to one responder.
- **Taken / Turned down / Missed**: the three ping outcomes. Missed means the ping wait ran out. Turned down and missed are recorded separately, and both move the callout on.
- **Ping wait**: one global value, set in the release. It's 60s since 4.2 (it was 90s).
- **Routing priority**: the rank score. Its inputs are proximity (a travel-time estimate since 4.1), availability, capability match and recent acceptance. Turning down *or missing* a ping lowers it.
- **Capability tags**: flight, structural-entry, hazmat-tolerant, cold-weather, aquatic, crowd-management, de-escalation.
- **Cover identity**: a responder's public persona, with no mapping to a legal identity. **Mutual aid**: cross-area cover. It isn't supported and is on the Q4 exploration list.

### The people
4.2 page comments use roles, not names. They map as shown.
| Person | Role (comment label) | Go to for |
| --- | --- | --- |
| Helen Achebe | Director of Product (my director), Site Aleph | Roadmap and commitments |
| Marcus Oyelaran | Engineering manager, Site Aleph | Engineering. Straight talker. Start here. |
| Wen Li | Staff engineer, Berlin | Built routing ranking. The only real source on how it works. |
| Sofia Marino | Product designer, Site Aleph | Console and phone app. Ran the Sept interviews. |
| Ravi Menon | Data analyst, Singapore | **Owns the official weekly acceptance numbers** |
| Nadia Hoffmann | Support lead, Berlin | Tickets. Hears about handler pain first. |

Priya's handoff says to get numbers from Marcus. Use Ravi's instead. Marcus himself called his "rough." On the customer side, the handlers are Aunt Dot (Vesper), Mr. Ambrose (Captain Vantage), Halloran (Sgt. Bulwark, and also a loud Supply voice) and Kip (Meteor Mite and The Gale).

### Where things stand
**4.2 (shipped 12 Aug) is the live issue.** It made two routing changes at once: proximity weighted up against acceptance history, and the ping wait cut from 90s to 60s. It also made console filters persist.
- Tickets ran at ~3x normal and were still flat on 26 Aug. About ⅔ say "phone never goes off" and ⅓ say "gone before I could answer." Nadia could explain the second but not the first. A handler emailed support directly, which "never happens."
- On 14 Aug, Marcus asked whether the routing change was *meant* to hit responders who've been turning jobs down. Wen was away 14–24 Aug, and the question was never answered on the page.
- Priya's view, which Marcus echoes, is that it's a seasonal August dip, so don't revert. **The data contradicts this (see below).**
- The team agreed to regroup on 4.2 once the new PM was up to speed.

**My Session 1 findings (rook-database, 29 Jun–6 Sep):**
- The share of pings taken was 75–78% every week through 3 Aug, then fell to 54% in release week. That doesn't fit a seasonal dip. Missed pings went from ~4 to ~38 a week. Tickets went from ~6 to 20–32 a week.
- Farlight, Meteor Mite, The Undertow and Vesper dropped from ~12 pings a week to ~1. The Gale (same area and handler as Meteor Mite) rose to 21. That's the "phone never goes off" ⅔.
- **Cause, from the code:** `history.py` `record_declined()` treats missed the same as turned down (−0.12, vs +0.08 for a yes), and the score never decays (there's an open TODO from 2019). So the 60s wait led to misses, misses lowered rank, lower rank meant no pings, and with no pings there was no way to recover. This answers Marcus's question: it wasn't a decision.
- Sofia's Sept interviews (Dot, Ambrose, Kip, Halloran) independently describe both the "gone too fast" and "too quiet" patterns.
- Gaps: there's no data before 29 Jun (so no year-over-year check), and no tags, availability or travel times. Ravi's reports aren't in the wiki. There's a possible Supply side-effect: locked-out responders look idle to maintenance scheduling.

**The roadmap is stale.** It was last reviewed 30 Jun and every item is still owned by "Priya":
- *Change to who gets pinged* and *Ping timeout tuning* (4.2) both shipped but are still marked Committed. The timeout shipped as a flat 60s cut.
- *Availability Confidence* (4.2, Committed) isn't in the 4.2 release notes, so it was squeezed out. Whether it's still a Q3 commitment needs a conversation with Helen.
- *Requisition approval chains* (4.3, Supply) has a core ask of a 2nd approval over $2,000. Its brief creeps into a reroute, a dashboard, handler visibility and a spreadsheet replacement.
- *Handler phone app* and *Shared cover between responders* are Q4, Exploring.

**Briefs vs. Releases:** briefs aren't updated after shipping. The Override Audit Log shipped in 4.0 and Bulk Callout in 4.1, yet Bulk Callout's brief still says it's unclaimed. The Handler Phone App brief (8 Sep, Sofia) is unstaffed.

### Open to-dos
1. Take the findings to Ravi (to confirm with official numbers) and to Marcus and Wen (to confirm the mechanism).
2. Decide the fix: separate missed from turned down, add decay, or revisit the 60s wait. Check any fix for Supply impact.
3. Settle the Q3 commitments with Helen, including Availability Confidence, and refresh the roadmap.
4. Write the missing doc on how routing decides who gets pinged, with Wen.
5. Expect noise tickets from filter persistence. Don't let them eat the first month.

### How to help me
- Use Ravi's numbers for metrics. Keep turned down, missed and coverage gap separate. Separate the two 4.2 routing changes.
- Flag anything touching the Responder Availability Record as a Supply impact.
- When the wiki contradicts itself, trust Releases for what shipped and name the source you used.
- Decision framed (28 Oct session): stop counting missed pings as turned down, plus a one-time score reset for the 4 locked-out responders. Keep the proximity change and leave the 60s wait for later. 4.2 actually *cut* acceptance weight 0.40→0.25 (`config.py`), so reverting the proximity change wouldn't fix this. Helen (`05-super-speed/director-request.txt`) wants a one-pager plus a clickable prototype before any code changes, so take the decision to her.
- Pings after 4.2 for the 4 locked-out responders: 7–9 missed but only 2–3 turned down each. They took 69–81% before. Unexplained: The Gale also missed 8 but kept 69 pings. Ask Wen why.
- Callouts nobody took went from ~8/wk to ~13/wk after 4.2 (97 total). The DB can't split these into coverage gaps, because it has no tags and no answer times, so time-to-accept can't be computed either.
- Cross-area pings went from 17% to 39% after 4.2, even though mutual aid is "not supported." Locked-out responders' work spills to neighbours. Supply books maintenance by callout load, so routing affects Supply even without touching the availability record.
- All 83 open tickets are post-release (24 of 107 closed). No Supply contact is named anywhere. Data stops 6 Sep, so check 4.3/4.4 and Ravi's latest before acting.
- Interviews vs tickets (Module 2): only Ambrose appears in both (1 ticket, #3043). Dot, Kip and Halloran filed none, and the other 146 tickets come from 11 handlers who weren't interviewed. So the two piles mostly cover different handlers.
- Tickets (147: 40 before 12 Aug, 107 after, 83 open): 30 "phone went quiet" (Farlight, Undertow, Ashgrove, Halfmoon only), 15 "gone before I could answer", 16 saved filters, 26 Supply, and the rest are older console friction. Many tickets repeat word for word, so ask Nadia whether that's a macro or template before treating them as 45 separate incidents.
- Interviews (Sofia, 2–5 Sep): "gone too fast" 3/4, "tell the handler when a callout comes in" 3/4 (0 tickets ask for this), text too small 2/4, too quiet 2/4. The interviews underweight the lockout, which the tickets show is the bigger problem.
- Ashgrove and Halfmoon dropped less sharply (10–11 to 6–8 pings a week) and aren't on the Session 1 locked-out list. Check them in pings. Meteor Mite and Vesper have no tickets.
- 4 tickets (an "on leave" status, availability time zones) touch the Responder Availability Record, which is a Supply impact. Suggest Linda Pruitt (Farlight) and Desmond Okafor (Undertow) to Sofia as the next interviews.
- Module 3 (pings + support_tickets, 29 Jun–7 Sep): the 4.2 change was immediate. Misses jumped on release day (week before: 3; 12 Aug: 7; 13 Aug: 12), and pings after a miss moved on after 61–63s, down from 92–93s, so the 60s wait was live from day one. Ping volume barely moved (24.7 to 23.5 a day), and neither time of day nor how many pings a responder got explains the misses.
- Every responder's miss rate rose (median 2.4% to 15.5%). The other 12 got 16–43% more pings each. Only the locked-out 4 collapsed (−66 to −75%), and they already missed 40–57% in release week, versus 8–33% for everyone else. Stormwrack and Nightwell had similar first weeks but weren't locked out: ask Wen what kept them ranked.
- Tickets vs pings: "too fast" tickets move the same day as misses, and all 15 match a real miss. "Quiet" tickets lag by about 5–8 days and peaked in the week of 26 Aug, while the share of pings taken recovered (54%, 62%, 68%, 75%). That recovery is partly because the locked-out 4 stopped being pinged. 62 of the 107 post-release tickets aren't about routing. Ticket day counts ("not gone off in 6 days") match the pings exactly.
- Stopped using "54% taken" as the headline: it's a one-week low and can't see the lockout. Use two numbers instead: missed 2% to 23% (still about 12%), and 4 responders locked out (about 51 to 2 pings a week), with Ashgrove and Halfmoon down about 20–30%.
- Case study: Vesper took 81% of pings before (the best of the 4), went 23 pings without a miss before the release, then missed 4 of the first 9 after it. Last acceptance was 17 Aug, and Vesper got 0 pings on 2–6 Sep. Vesper has no tickets, so ask Aunt Dot. The ticket tables have no times or callout numbers, and the ticket groups come from subject lines.
