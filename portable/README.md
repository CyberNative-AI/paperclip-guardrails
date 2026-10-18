# Portable rules for any coding or business agent

The same failure→rule pairs, written for one agent and one operator instead of a Paperclip company: Codex, Claude Code, OpenClaw or anything else that reads `SKILL.md` or `AGENTS.md`. Each skill starts with the failure that made us write it.

| Skill | What it stops | Field note |
|---|---|---|
| [`done-needs-evidence`](done-needs-evidence/SKILL.md) | "Done" that was never checked where it matters: a release that passed every check and failed live | July 2026 |
| [`two-strikes-then-change`](two-strikes-then-change/SKILL.md) | The same failure retried unchanged: 80 runs in 3.4 hours, 79 failed | August 2026 |

## Install

- **Codex:** copy a skill folder into `.agents/skills/` in your repository, or `~/.agents/skills/` for all projects.
- **Claude Code:** copy it into `.claude/skills/` in your repository, or `~/.claude/skills/`.
- **Always on:** the agent picks a skill when its description matches the task, or you can name the skill to call it. To keep a rule in force on every task, each `SKILL.md` also gives one line to paste into `AGENTS.md` or `CLAUDE.md`, which is always in context.

## Limits

These are instructions, not a patch to your agent. A model can still ignore one; the checks at the end of each skill tell you whether it is holding. A loop that starts outside the agent, in a cron job or script, needs whoever runs it to fix that automation.

## More rules

The other four rules in this set (time limits on commands, search before create, state in a file, ask once) are in Pack 1, written for Paperclip companies, among its twelve pairs: <https://cybernative.ai/paid-for/?ref=gh-portable> ($19/month or $49 once).
