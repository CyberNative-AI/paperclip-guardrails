---
name: two-strikes-then-change
description: >
  Use when setting up a routine, script or scheduler that assigns or wakes
  work, when a command, API write, build, deploy, login or whole approach has
  failed and you are about to try it again, or when you are woken to retry or
  continue work that failed before. Limits retries and says what to do
  instead.
key: cybernative-ai/paperclip-guardrails/two-strikes-then-change
tags:
  - paperclip
  - guardrail
---

# Two strikes, then change

## The failure (field note, 2026-08)

In August 2026 a scheduler kept handing an issue back to an agent every ten
minutes while automatic recovery kept taking it away. Every handoff started a
new run, and every run hit the same shared model usage limit. Nothing asked
whether the last attempt had failed for the same reason. In 3.4 hours there
were 80 runs: 79 failed, 1 succeeded, no comments, and a second automation
flagged the agent as unproductive, twice. The limit then cleared on its own.
The cost page showed $0, because the runs were on a flat-rate subscription;
the real cost was a shared usage window the rest of the company needed.

No agent instruction could have stopped this one: each of the 79 runs failed
before the agent did anything. What would have stopped it is the first half of
this rule, written for whoever runs the automations. The second half is the
same rule one level down, for an agent about to repeat a step that already
failed for the same reason.

## The rule

After two similar failures, change something real or stop and hand the
problem to someone who can fix it. Never retry a deterministic failure
unchanged. Never work around a refusal or a permission denial.

## How to apply it in a Paperclip run

For whoever runs automations (routines, scripts, schedulers, external
engines that assign issues):

1. An automation that assigns or wakes work must read the issue's recent runs
   first and stop after two failures of the same kind, instead of handing
   the issue back again.
2. Never let two automations move the same issue: if recovery has taken an
   issue away from an agent, a scheduler must not give it back.
3. Treat a rate or usage limit as a pause until its reset time, not as a
   failure to retry.

For an agent that hit a failure or was woken to retry:

4. **Classify the first failure.** Transient: timeout, 429, 502/503, reset.
   Deterministic: 401/403, 404, 409, 422, validation error, missing file or
   key, wrong config, a refusal. Otherwise unknown.
5. **Deterministic:** fix the cause (correct input, read current state, use
   the permitted route) or stop. Do not retry unchanged.
6. **Transient:** retry once, with a short wait inside your time limit.
7. **Second similar failure:** change the approach, the input size or the
   owner. A new run, a new session or a new comment is not a change.
8. **Still failing:** set the issue `blocked` with the exact error, what you
   tried, and who can fix it. Do not retry in the next run unless something
   changed.
9. **Count across runs.** If you were woken to retry or continue, read the
   issue's recent runs (`GET /api/issues/{id}/runs`) and comments first. Two
   earlier failures of the same kind mean you are at step 8.
10. **Read back before retrying a write** that may have succeeded (creates,
    payments, sends, posts).
11. A refusal, permission denial or safety block is a boundary. Do not switch
    account, tool, user agent or route to get around it.

Install as a skill, or paste this line into each agent's `AGENTS.md` so it is
always in context: *"After two similar failures, change the approach or set
blocked with the error and an owner; never retry a deterministic failure
unchanged; never work around a refusal or permission denial."*

## How to tell it is working

- `GET /api/companies/{companyId}/heartbeat-runs`: group failed runs by issue
  and `errorCode`. No issue should show three or more failures with the same
  code in a row without a change of owner or status between them.
- Issues that reach `blocked` after repeated failures should name the error
  and an owner in the blocking comment.
- Runs per issue per hour: any issue with more than a handful of runs in an
  hour and no status change is a loop; find the automation that starts them.

## Limits

- Paperclip retries some failures by itself: transient upstream errors get
  two automatic retries 30 seconds apart, and provider quota errors are
  retried after the reset time when Paperclip can read it. A run timeout is
  not retried. Those retries happen before any agent instruction
  can act.
- A loop caused by two automated systems handing work back and forth (a
  scheduler and recovery, as above) needs the operator to fix one of them.
  This rule only stops the agent from adding to it.
- "Similar" is a judgement. Agents can disagree with you about whether two
  errors are the same; the run log shows it.
