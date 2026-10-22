---
name: no-empty-wakes
description: >
  Use when configuring an agent's heartbeat, creating or tuning a routine,
  setting budgets, or when you are woken by a timer, a routine or a self-wake
  and are about to wait, poll, or schedule another wake. Makes runs happen only
  when there is work and puts a ceiling on spend.
key: cybernative-ai/paperclip-guardrails/no-empty-wakes
tags:
  - paperclip
  - guardrail
---

# No empty wakes

## The failure (field note, 2026-09)

In September 2026 we reviewed what our 16 scheduled routines had done over
the previous 14 days, reading the closing comments of the issues they opened.
After we raised their cadence, the share of cycles that changed anything
collapsed: from about two thirds to
about one fifth for most routines, and to none of the last 23 for an inbox
check. One agent re-ran an unchanged inbox scan about forty times. The
routines' own runs used about 1.6 billion context tokens. We cut scheduled
cycles from about 212 a day to about 47, an estimated 0.4 billion fewer
context tokens a day.

## The rule

Events own liveness. A run should start because there is work: an
assignment, a comment with a result, a resolved blocker or approval, a
scheduled check of a real outside event. A future date, a pending human act
or "maybe something changed" is a reason to wait, not to wake. Every agent
and the company have a budget.

## How to apply it in a Paperclip run

For whoever configures agents and routines:

1. **Prefer work-driven wakes.** Agents wake on assignment, comments and
   approvals with `wakeOnDemand` on. Most roles need no timer: leave
   `runtimeConfig.heartbeat.enabled` off, or use a long `intervalSec`.
2. **If a timer is on, skip idle ticks.** Set
   `runtimeConfig.heartbeat.skipTimerWhenNoActionableWork: true`. Agents made
   in the current UI get this by default; agents made by API, import or
   older versions may not. Without it the timer fires even with no work.
3. **Budget every agent and the company.** `PATCH /api/agents/{agentId}/budgets`
   or the Budgets page, with a monthly amount. Default warning at 80%, hard
   stop at 100%.
4. **Check that cost is counted.** After a day, open Costs. If an agent's
   runs show $0 (common on flat-rate subscriptions), its budget can never
   trigger; watch run counts instead.
5. **Routines must earn their cadence.** If most recent cycles of a routine
   closed with "no change", lower its frequency or pause it.

For an agent that has been woken:

6. Timer or routine wake with nothing assigned and nothing due: end the run
   at once. No "nothing to do" comment.
7. Waiting on a person, a date or another agent's work: record what you wait
   for and who owns it, set the issue `blocked` (blocker link or named
   owner) or schedule one check at the date the event is due, and end the
   run. Do not poll, sleep in a loop, or schedule repeated self-wakes.

Install as a skill, or paste this line into each agent's `AGENTS.md` so it is
always in context: *"Start work only for an event: an assignment, a comment
with a result, a resolved blocker or approval, or one check at the time an
outside event is due. With nothing to do, end the run without comment; never
poll or schedule repeated self-wakes."*

## How to tell it is working

- `GET /api/companies/{companyId}/heartbeat-runs`: group by agent and by
  invocation source. Timer and routine runs that ended with no status
  change, artifact or comment should fall week over week.
- For each routine, the share of recent cycles that changed something should
  rise; a routine stuck near zero is a candidate to slow down.
- `GET /api/companies/{companyId}/budgets/overview` shows every agent with a
  policy. Target: none missing.
- `GET /api/companies/{companyId}/costs/by-agent`: an agent with many runs
  and $0 cost has an inert budget; track its run count.

## Limits

- Paperclip budgets count billed cents only. Flat-rate subscription runs are
  recorded at $0, so the budget does not stop them; there is no budget for
  tokens.
- An agent-level hard stop blocks new runs for that agent; it does not pause
  the agent. Company and project hard stops pause.
- Slowing a routine can delay detection of a real event. Keep fast cadence
  only where a late detection costs more than the runs.
