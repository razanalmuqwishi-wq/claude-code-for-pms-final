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

I'm the new PM for **Rook Dispatch**, taking over from Priya Raghunathan (left 21 Aug 2026, no overlap). Sources: rook-wiki (About Rook, Glossary, Team directory, Releases, Q3 roadmap, Product briefs), `00-rook/company/notes/handoff-from-priya.docx`. Routing code lives in `00-rook/code/dispatch-routing/`.

### The company
- Rook Industries sells coordination and provisioning software to independent masked **responders** and the **handlers** and **quartermasters** who support them. Subscription priced per active responder. Ships on a monthly release train (4.x).
- **Hard rule:** Rook never stores or reconstructs a responder's legal identity, only capability tags, availability and callout history. Never design or analyze anything that tries to work out who someone is (see Security Policy 4.1).

### The products
- **Rook Dispatch** (mine): ranks available responders for an incident, pings the top one's phone, and moves down the list on turned down/missed. Handlers use the web console. Responders use the phone app. Routing config ships *in the release*, so handlers can't tune it.
  - Metrics: **acceptance rate** (headline, weekly), **time-to-accept** (median s), **coverage gap** (nobody *could* go, which is different from nobody *would*).
- **Rook Supply**: requisitions, quartermaster approvals, maintenance schedules, field failure reports.
  - **Shared dependency:** Supply reads the **Responder Availability Record**, which Dispatch writes, to place maintenance in low-callout windows. Any Dispatch change to availability lands in Supply without warning.

### Vocabulary (Rook-specific meanings)
- **Callout**: request to attend an incident. **Ping**: a callout offered to one responder.
- Ping outcomes: **Taken**, **Turned down**, or **Missed** (ping wait ran out). Turned down and missed are recorded separately.
- **Ping wait**: how long a ping waits before it's missed. Global, set per release. Now 60s (was 90s).
- **Routing priority**: the rank score. Inputs are proximity (travel-time estimate), availability, capability match and recent acceptance history. Turning down *or missing* a ping lowers future rank.
- **Capability tag**: e.g. flight, structural-entry, hazmat-tolerant, cold-weather, aquatic, crowd-management, de-escalation.
- **Cover identity**: public persona. Rook holds no mapping to a legal identity.
- **Mutual aid**: cross-area cover. Not supported. On the Q4 exploration list.

### The people (Dispatch)
| Person | Role (as wiki comments label them) | Go to for |
| --- | --- | --- |
| Helen Achebe | Director of Product (my director), Site Aleph | Roadmap and commitments |
| Marcus Oyelaran | Engineering manager, Site Aleph | Engineering. Straight talker. Start here. |
| Wen Li | Staff engineer, Berlin | Built the routing ranking. The only real source on how it works. |
| Sofia Marino | Product designer, Site Aleph | Console and phone app. Ran the September interviews. |
| Ravi Menon | Data analyst, Singapore | **Owns the official weekly acceptance numbers** |
| Nadia Hoffmann | Support lead, Berlin | Tickets. Hears about handler pain first. |

Customer-side names in the wiki: handlers Aunt Dot, Mr. Ambrose, Halloran and Kip. Halloran is also a frequent Supply voice. There is a Customer interviews database (Sept) that I haven't read yet.

### Where things stand (as of the wiki, late Aug–Sep 2026)
**4.2 (shipped 12 Aug) is the live issue.** It weighted proximity more heavily against acceptance history and cut the ping wait from 90s to 60s. Both changes shipped at once.
- Support tickets were ~3x normal after release and were still elevated on 26 Aug. The split is about ⅔ "my phone never goes off" and ⅓ "gone before I could answer." Nadia can explain the second (shorter wait). **Nobody has explained the first.** A handler emailed support directly, which "never happens."
- Marcus asked whether the routing change was *meant* to apply to responders who've been turning jobs down. The config doesn't distinguish them. Wen was away 14–24 Aug. **This is still unanswered.**
- Priya's view, in her comments and handoff: mostly a seasonal August dip, so don't revert. Marcus has echoed it. **This hasn't been tested against data.** Only "rough" numbers from Marcus were offered, and Ravi, who owns the real weekly figures, hasn't been looped in on the page.
- My hypothesis to check, not a fact: a shorter wait means more missed pings, which lowers rank, which means fewer pings. That loop could explain "phone never goes off."
- The team agreed to regroup on 4.2 once the new PM was up to speed.

**Roadmap is stale** (last reviewed 30 Jun; every item is still owned by "Priya"):
- *Change to who gets pinged* (4.2): shipped.
- *Ping timeout tuning* (4.2): shipped as a flat 90s to 60s cut, but still listed as Committed.
- *Availability Confidence* (4.2, Committed): **not in the 4.2 release notes, so it was squeezed out.** It's unclear whether it's still a Q3 commitment. Priya said this conversation with Helen "needs to happen."
- *Requisition approval chains* (4.3, Committed): a Supply item. Its brief has heavy scope creep: the core ask is a 2nd approval over $2,000, plus a reroute, a dashboard, handler visibility and a spreadsheet replacement.
- *Handler phone app* and *Shared cover between responders*: Q4, Exploring.

**Briefs vs. reality:**
- Routing Override Audit Log shipped in 4.0. Bulk Callout shipped in 4.1, but its brief still says nobody has picked it up. Briefs aren't updated after shipping, so check Releases.
- Handler Phone App brief (8 Sep) is owned by Sofia. Exploring. Not staffed.

**Open to-dos inherited from Priya:**
1. Get the real 4.2 numbers.
2. Settle the Q3 commitments with Helen.
3. Write the missing doc on how routing decides who gets pinged (with Wen).
4. Expect noise tickets from 4.2 filter persistence. Don't let them eat the first month.

### How to help me
- Treat Priya's "it's seasonal" as a claim to test. Separate the two 4.2 changes, and check year-over-year August data.
- Use Ravi's numbers for metrics. Keep turned down, missed and coverage gap apart.
- Flag anything that touches the Responder Availability Record as a Supply impact.
- Where the wiki contradicts itself, trust Releases for what shipped and say which source you used.
- Session 1 finding (rook-database, 29 Jun–6 Sep): share of pings taken held at 75–78% every week through 3 Aug, then fell to 54% in release week, so "seasonal" doesn't fit. Missed pings went from ~4 to ~38 a week. Tickets went from ~6 to 20–32 a week.
- Farlight, Meteor Mite, The Undertow and Vesper fell from ~12 pings a week to ~1 after release. The Gale (same area and handler as Meteor Mite) rose to 21. This is the "phone never goes off" ⅔ of tickets.
- Likely cause, from the code: `history.py` penalizes missed pings the same as turned down (−0.12 vs +0.08 for a yes) and the score never decays. So the 60s ping wait caused misses, then a lower rank, then no pings, then no chance to recover. This answers Marcus's 14 Aug question: it wasn't a decision.
- Gaps: the database has no capability tags, availability or travel times, and no data before 29 Jun, so there's no year-over-year check. Ravi's weekly reports aren't in the wiki. Possible Supply side-effect: locked-out responders look idle to maintenance scheduling.
- Sofia's Sept interviews (Dot, Ambrose, Kip, Halloran) independently describe the "gone too fast" and "too quiet" patterns.
- Open: take these numbers to Ravi and Marcus/Wen; decide the fix (separate missed from turned down? add decay? revisit the 60s wait?); settle Availability Confidence with Helen.
