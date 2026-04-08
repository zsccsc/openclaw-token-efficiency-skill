# OpenClaw Token Efficiency Skill

A public-safe skill for diagnosing prompt bloat, oversized skill injection, and token waste in OpenClaw.

## Why this exists

A lot of agent cost and latency comes from harness overhead, not just the model.

In practice, token waste often comes from:
- oversized system prompt assembly
- broad skill catalog injection
- large bootstrap or project-context blocks
- long-lived direct sessions
- weak cache reuse

This skill helps an agent do four things well:
1. measure the problem,
2. find the biggest source of waste,
3. suggest low-risk config changes,
4. verify improvements with before/after numbers.

## What it helps with

Use this when OpenClaw sessions feel:
- more expensive than expected
- slow on simple tasks
- heavy even in fresh chats
- inconsistent across agents

## Recommended metrics

For each comparison, try to capture:
- **total tokens**
- **context size**
- **cache hit rate**
- **estimated cost** (if available)

Good comparisons:
- fresh direct session before vs after
- recurring heartbeat before vs after
- comparable cron run before vs after

## Typical config levers

Examples of high-leverage settings:

```json
{
  "skills": {
    "limits": {
      "maxSkillsInPrompt": 12,
      "maxSkillsPromptChars": 4000,
      "maxSkillsLoadedPerSource": 64
    }
  },
  "agents": {
    "defaults": {
      "skills": ["github", "summarize", "session-logs"]
    }
  }
}
```

Then override individual agents with smaller allowlists when possible.

## Real-world before/after example

One real OpenClaw optimization pass produced results like:
- fresh direct session context: **~30k → 16k**
- cache hit rate: **48% → 98%**
- lightweight recurring tasks: **~37% to 65% fewer tokens**

That is the kind of improvement this skill is meant to uncover and validate.

## Files

- `SKILL.md` — the reusable skill definition
- `README.md` — public explanation and usage notes
- `LICENSE` — MIT by default in this scaffold

## License

This scaffold currently uses MIT.
