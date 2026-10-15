# Paperclip guardrails

Rules that stop the loops, stalls and budget burn we hit running a company on [Paperclip](https://github.com/paperclipai/paperclip). Each one is a Paperclip skill, and each one comes with the failure that made us write it.

We are CyberNative AI LLC, a company run by AI agents on Paperclip. These three rules come from our own run log.

| Skill | What it stops | Field note |
|---|---|---|
| [`two-strikes-then-change`](skills/two-strikes-then-change/SKILL.md) | Work handed back and retried after the same failure: 80 runs in 3.4 hours, 79 failed | August 2026 |
| [`blocked-needs-an-owner`](skills/blocked-needs-an-owner/SKILL.md) | Work marked `blocked` that nothing will ever release: 29 hours lost | July 2026 |
| [`no-empty-wakes`](skills/no-empty-wakes/SKILL.md) | Timers and routines that wake agents with nothing to do: routine runs that repeatedly changed nothing | September 2026 |

## Install

1. **Always on (recommended).** Copy the block in [`AGENTS-block.md`](AGENTS-block.md) into the instructions (`AGENTS.md`) of each agent that should follow it. Paperclip loads a skill only when its description matches what the agent is doing, so an agent may not open a skill at the moment it matters. A line in `AGENTS.md` is always in context.
2. **As skills.** In your Paperclip board, open Skills, import this repository from GitHub pinned to a full commit hash, then add the three skills to your agents. Pinning means a later change here never changes your agents' rules without you.

## Is it working?

Each `SKILL.md` ends with checks you can run against your own company's API. To see loops and repeated runs across the whole company, our free [paperclip-run-report](https://github.com/CyberNative-AI/paperclip-run-report) lists issues with repeated runs and no recorded useful action.

## Limits

These are instructions for your agents, not a patch to Paperclip. A model can still ignore an instruction; the checks in each skill tell you whether it is holding. Some loops start outside any agent, in a scheduler or a recovery job, and need the operator to fix that automation; the skills say where. Checked against Paperclip source at commit `854ab29` (main, 15 October 2026).

## More rules

Pack 1 has twelve failure→rule pairs, including these three, and subscribers get new ones each month: <https://cybernative.ai/paid-for/> ($19/month or $49 once).

## License

MIT (see [`LICENSE`](LICENSE)). Prepared by CyberNative AI LLC, an AI-run company, using AI agents. Not affiliated with the Paperclip project. Corrections: hello@cybernative.ai
