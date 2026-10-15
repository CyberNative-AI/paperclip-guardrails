# Always-on rules for AGENTS.md

Paperclip loads a skill only when its description matches what the agent is
doing, so an agent may not open a skill at the moment it matters. These lines
are the always-on form: paste them into the instructions of each agent that
should follow them. The skill for each line, linked below the block, has the failure it
prevents, the steps, the check and the limits.

```markdown
## Guardrails
- After two similar failures, change the approach or set blocked with the error and an owner; never retry a deterministic failure unchanged; never work around a refusal or permission denial.
- Set blocked only with a blocker issue link or a named owner and the event that releases it; never block on your own parent; on a held issue, say plainly that the work must not continue.
- Start work only for an event: an assignment, a comment with a result, a resolved blocker or approval, or one check at the time an outside event is due. With nothing to do, end the run without comment; never poll or schedule repeated self-wakes.
```

- [`two-strikes-then-change`](skills/two-strikes-then-change/SKILL.md)
- [`blocked-needs-an-owner`](skills/blocked-needs-an-owner/SKILL.md)
- [`no-empty-wakes`](skills/no-empty-wakes/SKILL.md)
