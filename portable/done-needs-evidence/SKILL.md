---
name: done-needs-evidence
description: >
  Use before telling the user that work is done, works, shipped, is deployed
  or is fixed, and before ending a task. Says what evidence the final message
  must carry and which word to use for how far the work got.
---

# Done needs evidence

## The failure (field note, 2026-07 and 2026-08)

In July 2026 a release passed an exact-hash security review, custody checks
and the full test suite, then failed the moment it went live: the production
site's content security policy blocked the page's own scripts, so it could
never have worked there. Every check had compared the artifact with itself,
not with the place it was going. It cost 40 minutes and a rollback. Days
later a seven-day measurement woke to score a release that had never gone
live; the public page still showed the old version.

## The rule

Done means the outcome the user asked for was checked where it matters. The
final message names what changed, how it was checked and with what result,
and the level reached: prepared, approved, deployed, working or used.
Unknown is not passed.

## How to apply it

Before you report the task as done, your final message contains:

1. **What changed:** the artifact (commit, file, URL, sent message).
2. **How you checked it:** the exact check and its result, for example
   `GET https://example.com/pricing (logged out) → 200, shows "$19/month"`.
   Check the real destination, not your own copy, and that it serves the
   bytes you tested.
3. **The level reached**, one word:
   - `prepared`: the artifact exists but has not left your side;
   - `approved`: the named reviewer accepted it;
   - `deployed`: it is live where users reach it;
   - `working`: you watched it do its job in the real place; name the
     path and inputs you exercised, and the rest stays unknown;
   - `used`: someone other than you and the user used it.

If the user asked for `working` and you only have `prepared`, it is not
done: say so, and name the next step and who can take it. A check that
could not run is `unknown`, with the reason: if the deploy went out and only
the check in the real place could not run, report `deployed`, working
unknown, not `prepared`. "Run succeeded", "exit 0", "should work" and
"looks good" are not evidence.

To keep it in context all the time, paste this line into `AGENTS.md` or
`CLAUDE.md`: *"Report done only with a final message that names the
artifact, the check you ran where it matters and its result, and the level
reached: prepared, approved, deployed, working (for the path and inputs you
exercised) or used; unknown is not passed."*

## How to tell it is working

- Read the final messages of your last ten finished tasks. Each should name
  an artifact, a check with its result, and one of the five level words.
- Count tasks you had to reopen, or follow with a "fix" task, within seven
  days. It should fall.
- Releases: "passed the tests, failed live" should go to zero once checks
  run against the destination.

## Limits

- An agent can still write a check it did not run. Repeat the check
  yourself now and then.
- Some outcomes cannot be seen yet when the task ends (a measurement over
  seven days). Close at the honest level and schedule the later check.
- A check is only as good as the destination it reads. If the agent cannot
  reach production, it cannot report `working`: the honest report is
  `deployed`, working unknown, when the deploy itself is confirmed, and
  `prepared` when it is not.
