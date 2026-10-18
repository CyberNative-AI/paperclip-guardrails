---
name: two-strikes-then-change
description: >
  Use when a command, API write, build, deploy, login or whole approach has
  failed and you are about to try it again, when you are restarted to retry or
  continue work that failed before, or when setting up a loop, cron job or
  script that restarts an agent. Limits retries and says what to do instead.
---

# Two strikes, then change

## The failure (field note, 2026-08)

In August 2026 a scheduler kept handing a task back to an agent every ten
minutes while automatic recovery kept taking it away. Every handoff started a
new run, and every run hit the same shared model usage limit. Nothing asked
whether the last attempt had failed for the same reason. In 3.4 hours there
were 80 runs: 79 failed, 1 succeeded, and a second automation flagged the
agent as unproductive, twice. The limit then cleared on its own. The cost
page showed $0, because the runs were on a flat-rate subscription; the real
cost was a shared usage window the rest of the work needed.

No agent instruction could have stopped this one: each of the 79 runs failed
before the agent did anything. What would have stopped it is the first half of
this rule, written for whoever runs the automation. The second half is the
same rule one level down, for an agent about to repeat a step that already
failed for the same reason.

## The rule

After two similar failures, change something real or stop and hand the
problem to someone who can fix it. Never retry a deterministic failure
unchanged. Never work around a refusal or a permission denial.

## How to apply it

For whoever runs a loop, cron job or script that starts the agent:

1. Before starting the agent again, read the last runs' exit status or log,
   and stop after two failures of the same kind instead of starting it again.
2. Never let two automations restart the same work: if one has stopped it,
   another must not start it again.
3. Treat a rate or usage limit as a pause until its reset time, not as a
   failure to retry.

For an agent that hit a failure or was started to retry:

4. **Classify the first failure.** Transient: timeout, 429, 502/503, reset.
   Deterministic: 401/403, 404, 409, 422, validation error, missing file or
   key, wrong config, a refusal. Otherwise unknown.
5. **Deterministic:** fix the cause (correct input, read current state, use
   the permitted route) or stop. Do not retry unchanged.
6. **Transient:** retry once, with a short wait inside your time limit.
7. **Second similar failure:** change the approach, the input size or the
   owner. A new session or a new prompt is not a change.
8. **Still failing:** stop and report the exact error, what you tried, and
   who can fix it. Do not retry in the next session unless something changed.
9. **Count across sessions.** If you were started to retry or continue, read
   the notes or log from earlier attempts first. Two earlier failures of the
   same kind mean you are at step 8.
10. **Read back before retrying a write** that may have succeeded (creates,
    payments, sends, posts).
11. A refusal, permission denial or safety block is a boundary. Do not switch
    account, tool, user agent or route to get around it.

To keep it in context all the time, paste this line into `AGENTS.md` or
`CLAUDE.md`: *"After two similar failures, change the approach or stop and
report the error and who can fix it; never retry a deterministic failure
unchanged; never work around a refusal or permission denial."*

## How to tell it is working

- Group failed runs in your loop's log by error. No task should show three or
  more failures with the same error in a row without a change between them.
- Stopped work should name the error and who can fix it.
- Runs per task per hour: more than a handful in an hour with no change in
  the result is a loop; find the automation that starts them.

## Limits

- Your agent tool or API client may retry some failures by itself before any
  instruction can act. Read its retry settings.
- A loop caused by two automated systems handing work back and forth (a
  scheduler and recovery, as above) needs whoever runs them to fix one of
  them. This rule only stops the agent from adding to it.
- "Similar" is a judgement. Agents can disagree with you about whether two
  errors are the same; the log shows it.
