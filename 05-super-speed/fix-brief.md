# Brief: A missed ping is not a "no"

**For:** Helen Achebe (Director of Product) · **From:** Dispatch PM · **Date:** 28 Oct 2026 · **Status:** Draft v2, stress-tested. No code changes until Helen signs off and the blockers below are cleared.

Sources: Helen's request (`05-super-speed/director-request.txt`), the wiki Glossary, Team directory and 4.2 release page and comments, Priya's handoff, `dispatch-routing/` (`config.py`, `history.py`, `offer.py`, `routing.py`, `availability.py`), and rook-database pings 29 Jun – 7 Sep. **All numbers come from my own queries and are waiting on Ravi's official weekly figures.**

---

## Who it's for

**Vesper**, a responder handled by **Aunt Dot**. Vesper was one of the most reliable people on the roster and went quiet without doing anything wrong.

- **Before 4.2:** took 81% of pings, the best of the four responders who were later locked out, and went 23 pings in a row without a miss.
- **After 4.2 (12 Aug):** missed 4 of the first 9 pings. The last accepted ping was on 17 Aug. By 2–6 Sep Vesper got **zero pings**.
- **What Dot told Sofia (3 Sep):** the ping is "gone to somebody else already" before Vesper can answer. It "didn't used to feel like a fair race" and now it's lost more often than won. Dot also noticed quiet stretches where "the phone just sits there", but "couldn't tell you why".
- Vesper and Dot filed **no tickets**. Neither knows that the misses caused the silence.

Vesper is one of four. Farlight, Meteor Mite and The Undertow went the same way (about 51 → 2 pings a week combined). The handler-side version of this is **Kip**, who has Meteor Mite dead quiet and The Gale "on fire" on the same screen with "nothing on the screen that helps me make sense of why".

## The problem

4.2 shortened the ping wait from 90s to 60s, so more pings ran out before anyone answered. In `history.py`, `record_declined()` scores **a miss the same as a turn-down** (−0.12, against +0.08 for a yes), and the score **never recovers on its own**. A yes is the only thing that adds points, and nobody gets a yes without being pinged. Each miss pushed Vesper lower, lower meant fewer pings, and with no pings there was no way back up. Replaying the scoring code over the real pings drives exactly these four to 0 between 27 Aug and 5 Sep.

The wiki already treats these as different events. The Glossary says missed and turned down are "recorded separately". The code counts them as one (`offer.py` passes both to `record_declined()`). Nobody decided that missing should cost a responder their place in line. It just happened, which answers Marcus's 14 Aug question.

## What should change for Vesper

| | Today | After the fix |
|---|---|---|
| **Vesper misses a ping** | The ping moves on and Vesper drops in rank without being told. | The ping still moves on. The phone says *"Missed. It's gone to the next responder."* (Wording deliberately makes no promise about ranking. See decision D2.) |
| **Vesper turns a ping down** | The ping moves on and Vesper drops in rank. | Under review (D3). We can't leave a quick "no" costing more than letting a ping expire. |
| **Vesper, right now** | Locked out with no pings since early Sep. | The score is recalculated under the new rule. Vesper and Dot are **told when it happens**, not when the next ping arrives: *"Your missed pings were counting against you by mistake. That's been corrected."* |
| **Aunt Dot / Kip on the console** | Two cards, one silent, and no explanation. | Each card shows *pings this week*, plus *last pinged* if it's been 3+ days. Wording must not suggest a fault when the area simply had no callouts (D5). |

Helen asked for "not a setting", so none of this is a control. Handlers and responders can't tune routing (it ships in the release). They can now *see* why someone is quiet, and missing a ping no longer silently costs a responder their place.

## How the fix works

1. **Split the penalty.** `offer.py` already knows whether a ping was turned down or missed. It passes that to `history.py`, and a miss no longer triggers the −0.12. The callout still moves on. How much a miss should cost, if anything, is decision D2.
2. **Recalculate scores by rule, not by list.** ~~Reset the four to neutral.~~ Replay every responder's ping history under the new rule and set each score to the result. This is better for two reasons:
   - **Neutral would keep Vesper behind.** A neutral reset is 0.5, but under the new rule everyone else sits at 0.76–1.0. Vesper would come back ranked behind any peer about 5–9 minutes further away.
   - **No hand-picked list.** The replay automatically covers Ashgrove, Halfmoon and anyone locked out after our data ends on 7 Sep.
   - It is one recorded run, logged in the Override Audit Log if that log can hold it (Wen to confirm). It is **not a repeatable "reset" button** (see F6).
3. **Show it.** Add the phone copy, the immediate notice to the affected responders and their handlers, and the console card lines. Sofia owns the wording. Nadia owns replies to the 30 open "phone went quiet" tickets.
4. **Measure it** (Ravi, weekly, before and after):
   - Missed and turned down, **separately**.
   - Pings per week for every responder, not an average.
   - Callouts nobody took, and how many pings each callout went through.
   - Coverage gap, counted separately.
   - How many responders are at the 1.0 ceiling.
   - **Answer times, which we don't log today.** We need to start logging them first. Without them we can't measure time-to-accept or see what the fix costs.

## How this could fail or recreate the problem

| # | Risk | How it bites | What the brief now does |
|---|---|---|---|
| **F1** | **Something else is capping the list** | `routing.py` says "nobody is removed here" and `dispatch()` loops the whole list. Yet 40 of 49 unfilled callouts after 4.2 ended after **one** ping, and Vesper often wasn't asked in Old Town even after a first "no". If a callout expiry, handler cancel or availability flag outside this folder is doing that, then **fixed scores won't bring Vesper's pings back**. | **Blocker.** Wen to find it before we commit to the fix. |
| **F2** | **A free miss creates a "ghost" at the top** | A responder marked available whose phone is off, or who just stops answering, keeps their score and stays nearest. Every nearby callout spends its first 60s on them. If callouts really only get 1–4 pings (F1), that wasted slot turns into an unfilled callout. It's the 4.2 lockout reversed: the callout suffers instead of the responder. | D2: decide on a guardrail. My recommendation: after 3 misses in a row with no answer, the console asks the handler "Is Vesper still available?". Routing never changes availability itself. |
| **F3** | **Acceptance history stops counting** | Replaying with misses free, **all 16 responders hit the 1.0 ceiling within their first 3–17 days**, and they end between 0.76 and 1.0. That spread moves the total ranking score by ≤0.06. In practice ranking becomes "nearest first". That quietly settles the proximity vs. history debate 4.2 was about, without anyone deciding it. | D4: Helen and Wen decide whether that's acceptable. If it isn't, the fix needs the decay / earn-back question answered (Wen's 2019 TODO), not just the miss split. |
| **F4** | **Letting a ping expire becomes the smart move** | A "no" costs −0.12 and letting it expire costs 0. A responder who can't go is better off ignoring the ping, which wastes 60s for the next person in line. The "gone too fast" problem gets worse for everyone else. | D3: a turn-down should cost **no more** than a miss. Options are to cut the turn-down penalty or give a miss a small one. |
| **F5** | **The 60s race is unchanged** | Misses are still ~12% (against ~2% before 4.2). Vesper keeps losing the race Dot described. The score just stops punishing it. If we tell Vesper "fixed", Vesper will notice it isn't. | Say so explicitly in all wording. The 60s wait is the next decision, not out of scope forever. |
| **F6** | **The recalculation becomes a setting by the back door** | This is the first time a score has risen without a yes. Once it exists, "can you reset my responder?" becomes a support request, and that's the hidden setting Helen asked us not to build. | Run it once, by rule, recorded. No tool or console control. Nadia gets a script that says no. |
| **F7** | **Vesper's own availability may have changed** | From 19 Aug Vesper missed 4 of 5 *home* pings, after taking all 5 the week before. If Vesper is genuinely less available, the fix puts Vesper back on top, the misses resume (F2), and Dot sees the same lost race. | Ask Aunt Dot before ship. If availability is the answer, the fix is a better "available" status, and that's a **Supply impact**. |
| **F8** | **The Gale doesn't fit the story** | The Gale missed 8 after 4.2 and kept 69 pings. Stormwrack and Nightwell had first weeks as bad as the four and weren't locked out. If the mechanism is only part of the story, the fix only partly works. | Wen to explain before ship. |
| **F9** | **Where scores live is unclear** | `_scores` is an in-memory dict keyed by cover name, defaulting to 0.5. If production works like this, a restart already resets everyone to 0.5 and a renamed cover loses its history. Either one changes how the recalculation has to work. | Wen and Marcus to confirm how scores are actually stored. |
| **F10** | **The headline metric drops and looks like a regression** | More pings go to people who sometimes miss, so the share taken can fall even while lockouts end. | Agree with Ravi up front: success is "no responder locked out" plus misses, turn-downs and coverage gap tracked separately, not the weekly acceptance rate on its own. |
| **F11** | **Supply sees load move without warning** | Pings flow back to four or more responders and away from The Gale and neighbours. Cross-area pings (17% → 39% after 4.2) should fall. Supply books maintenance by callout load. | **Supply impact.** We need a named Supply contact before ship. The Responder Availability Record isn't touched, unless D2's guardrail or F7 changes that. |
| **F12** | **The data is seven weeks old** | Our data ends 7 Sep. 4.3 and 4.4 may have changed routing, and more responders may be locked out now. | Ravi's current numbers, and the 4.3/4.4 release notes, before any decision. |

## Decisions needed before build

| | Decision | Owner | My recommendation |
|---|---|---|---|
| **D1** | Is something outside the folder capping the list? (F1) | Wen | No build until answered |
| **D2** | What a miss costs: nothing, or a small penalty after several in a row, plus the handler prompt | Helen, with Wen | No score penalty. After 3 in a row, the handler gets an availability prompt. |
| **D3** | Make a turn-down cost no more than a miss (F4) | Helen, with Wen | Yes, in the same change |
| **D4** | Accept that acceptance history becomes near-inert, or fix earn-back/decay now (F3) | Helen, with Wen | Accept for this release, but decide decay within a quarter |
| **D5** | Copy for responders, handlers and ticket replies | Sofia, Nadia | Admit the fault, don't promise the race is fixed |
| **D6** | Release vehicle and owner | Marcus, Helen | The next 4.x train, not "this afternoon". Roadmap owner updated from Priya. |

## What it deliberately does not do

- **Doesn't change the 60s ping wait yet.** It's the next decision (F5), not a closed one.
- **Doesn't revert the proximity change.** 4.2 cut acceptance weight from 0.40 to 0.25 (`config.py`), so a revert doesn't fix this. Note that F3 means the fix leans further toward proximity anyway.
- **Doesn't let handlers edit scores** or add a routing setting, and it isn't a repeatable reset (F6).
- **Doesn't let routing change availability.** The D2 prompt asks the handler. The Responder Availability Record is only written the way it is today.
- **Doesn't take on** dark mode, bigger text, mutual aid, or separate alert sounds per responder.
- **Never uses or infers anyone's identity.** The brief works only with cover identities and callout history (Security Policy 4.1).

## Next

1. Wen: F1, F8, F9 (and whether handler overrides earn +0.08).
2. Ravi: current numbers, plus answer-time logging.
3. Aunt Dot: F7.
4. Helen: D2–D4 and D6, using this brief.
5. Sofia: the clickable prototype of the phone and console states above.
