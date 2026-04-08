---
name: openclaw-token-efficiency
description: Audit OpenClaw sessions for prompt bloat and token waste, then recommend low-risk config changes that shrink skill injection, reduce prompt weight, and improve cache reuse.
homepage: https://github.com/your-org/openclaw-token-efficiency-skill
metadata:
  {
    "openclaw": {
      "emoji": "🪶",
      "requires": {
        "bins": ["openclaw"]
      }
    }
  }
---

# OpenClaw Token Efficiency

Use this skill when an OpenClaw deployment feels too expensive, too slow, or suspiciously token-heavy.

This skill focuses on diagnosis first, then low-risk slimming changes.

## What this skill does

1. Inspect session usage, context size, and cache hit rates.
2. Identify whether waste is coming from long-lived sessions, oversized prompt assembly, broad skill injection, or poor cache reuse.
3. Recommend smaller per-agent skill allowlists and tighter global skill limits.
4. Validate improvements on fresh sessions or comparable recurring workflows.

## Recommended workflow

### 1) Collect baseline evidence

Check the sessions that matter most:
- user-facing direct sessions
- long-lived agent sessions
- recurring cron or heartbeat sessions

Look for:
- large context early in the session
- low cache hit rate
- high token use on short/simple tasks
- major differences between agents doing similar work

### 2) Diagnose the likely cause

Common causes:
- too many skills injected into prompt
- large skill descriptions or snapshots
- raw project/bootstrap files adding prompt bulk
- direct sessions reusing long, messy history
- agent configs inheriting skills they do not need

### 3) Apply low-risk slimming

Start with config changes before code changes.

Good first moves:
- lower `skills.limits.maxSkillsInPrompt`
- lower `skills.limits.maxSkillsPromptChars`
- lower `skills.limits.maxSkillsLoadedPerSource`
- define `agents.defaults.skills`
- override individual agents with narrower `skills` lists

Prefer the smallest skill set that still supports the agent's real job.

### 4) Verify on fresh runs

Do not trust config changes without measurement.

Compare before vs after on similar workflows:
- fresh direct session vs fresh direct session
- heartbeat run vs heartbeat run
- cron run vs cron run

Headline metrics:
- total tokens
- context size
- cache hit rate
- estimated cost when available

## Safety rules

- Prefer config changes over source patches first.
- Do not claim improvement from old sessions that still carry the old prompt state.
- Call out when a restart or fresh session is required.
- Avoid removing skills that an agent genuinely needs for its job.

## Good output format

Return 2 to 3 representative before/after datapoints with:
- workflow type
- before metrics
- after metrics
- percent or point change
- one plain-English takeaway

## Example recommendations

- "This agent keeps a broad default skill set but only uses summarize and github in practice. Narrow its allowlist."
- "This direct session is expensive because the base prompt is large before any real work begins. Measure a fresh session after reducing skill prompt limits."
- "Cache hit is healthy, but the stable prompt prefix is still too large. Shrink prompt inputs, not just cache behavior."
