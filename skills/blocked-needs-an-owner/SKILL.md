---
name: blocked-needs-an-owner
description: >
  Use before setting a Paperclip issue to blocked, when you find an issue that
  has been blocked for a long time, when you want help from your parent issue,
  or when a hold, pause or stop order applies to your work. Says what a valid
  block contains and how to release one.
key: cybernative-ai/paperclip-guardrails/blocked-needs-an-owner
tags:
  - paperclip
  - guardrail
---

# Blocked needs an owner and an event

## The failure (field note, 2026-07 and 2026-09)

In July 2026 a review sub-task closed with "changes requested". That did not
wake its parent, which sat `blocked` with no blocker link and nobody named to
act. Nothing could release it; it sat for about 29 hours until a person
noticed. Three days later an engineering agent whose run had ended used
`blocked` as a pause button and named itself as the one to unblock it. A
critical-path task sat dead for 3.3 hours. In September a worker tried to
block its task on its own parent to ask for help. Paperclip rejected the link
as a cycle, and automatic recovery escalated "a board decision is required"
to the human owner. A manager removed the routing obstacle; the worker then
completed a rework pass.

## The rule

Set `blocked` only when you can name who must act and what event releases
the block. Prefer a blocker link to another issue. Never block on your own
parent or ancestor. A comment on a held issue must say plainly that the work
must not continue.

## How to apply it in a Paperclip run

1. **A blocker issue exists:** set `blockedByIssueIds` to it. When every
   blocker reaches `done`, Paperclip wakes your issue's assignee for you.
2. **A person or another team must act:** name them and the exact action in
   the comment that sets `blocked` ("the operator must add the API key for
   X"). If your Paperclip version supports an unblock owner on the issue,
   set it; agents may only name themselves there, so for anyone else, assign
   or create an issue for them.
3. **Name the release event:** "when the blocker is done", "when the key
   exists", "on 2026-11-01". Not "later".
4. **Need help from your manager or parent?** Do not block on the parent.
   If the parent already waits on this child, Paperclip rejects the link as
   a cycle; if it does not, you have made a deadlock that nothing releases. Hand the issue to your manager for review, or comment on the
   parent if you are not its assignee.
5. **Only `done` releases a blocker.** A cancelled blocker still blocks. If
   you cancel the work you were waiting on, first update `blockedByIssueIds`
   to remove it (that update alone), then change the status.
6. **Re-blocking for a new reason? Clear the old blockers first.** If every
   issue in `blockedByIssueIds` is already `done` and you set `blocked`,
   Paperclip sends you a "blockers resolved" wake at once. Re-block again and
   it fires again: a loop. Remove the done blockers in the same update. If
   you are only waiting for a board answer, ask with a confirmation or
   question card that wakes you (`continuationPolicy: "wake_assignee"`) and
   set `in_review` instead of `blocked`.
7. **Block only what depends on the blocker.** Split out and continue the
   rest.
8. **Holds and stop orders:** when you comment on a held issue, say that the
   job must not continue, be replayed or be moved to another issue, and who
   can release it. Never give launch steps for held work. When a decision
   changes, post the new decision as a comment on the same thread; agents
   often read only the latest comment, so editing the description is not
   enough.
9. **Old blocks:** for an issue blocked more than a few days, check that the
   blocker or owner still applies. Unblock it, or hand it to your manager
   with the evidence.

Install as a skill, or paste this line into each agent's `AGENTS.md` so it is
always in context: *"Set blocked only with a blocker issue link or a named
owner and the event that releases it; never block on your own parent; on a
held issue, say plainly that the work must not continue."*

## How to tell it is working

- `GET /api/companies/{companyId}/issues?status=blocked&includeBlockedBy=true`:
  every blocked issue should have a blocker link, or a last status comment
  that names an owner and an event. Target: zero without either.
- For each blocked issue, `GET /api/issues/{id}/diagnostics/blockers` shows
  the chain. No blocker should point at the issue's own parent or ancestor,
  and none should be `cancelled`.
- The age of the oldest blocked issue with no named owner should stay under a
  day.

## Limits

- The rule makes blocks visible. It does not make the named owner act;
  someone still has to read the blocked list.
- Agents cannot assign unblock duties to other agents or to humans through
  the unblock-owner field. Naming them in a comment informs; only assignment
  or a new issue wakes them.
- An unresolved blocker returns an error if an agent tries to resume or start
  the blocked issue. That is intended; remove the stale blocker first.
