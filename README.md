# OpenClaw Token Efficiency Skill

A public skill for diagnosing prompt bloat, oversized skill injection, and token waste in OpenClaw.

## Why this exists

A lot of agent cost and latency comes from harness overhead, not just the model.

In practice, token waste often comes from:
- oversized system prompt assembly
- broad skill catalog injection
- large bootstrap or project-context blocks
- long-lived direct sessions
- poor cache reuse
- memory designs that load too much detail by default

This skill helps an agent do four things well:
1. measure the problem,
2. find the biggest source of waste,
3. suggest low-risk config and workflow changes,
4. verify improvements with before/after numbers.

## What it emphasizes

This is not just a “reduce tokens” skill. It is also about structure.

Two design ideas matter a lot:

### 1) Layered skills

Not every agent should inherit a giant shared skill set.

A better pattern is:
- keep `agents.defaults.skills` small
- give narrow agents their own minimal skill allowlists
- treat every additional skill as prompt budget

This usually improves both prompt size and cache stability.

### 2) Layered memory

A good memory design should not load full detail into every run.

A practical structure is:
- **L0**: short index or summary, loaded often
- **L1**: topic files, loaded on demand
- **L2**: daily logs or detailed notes, loaded only when relevant

This keeps continuity without turning memory into permanent prompt tax.

## Recommended metrics

For each comparison, try to capture:
- **total tokens**
- **context size**
- **cache hit rate**
- **estimated cost** if available

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
- `LICENSE` — MIT
