---
name: openclaw-token-efficiency
description: Audit OpenClaw sessions for prompt bloat and token waste, then recommend concrete config and workflow changes that shrink skill injection, slim memory/project context, and improve cache reuse.
homepage: https://github.com/zsccsc/openclaw-token-efficiency-skill
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

Use this skill when an OpenClaw setup feels too expensive, too slow, or suspiciously heavy even on simple tasks.

This skill is for harness-level efficiency work, not model benchmarking.

## Core idea

Token waste in agent systems usually comes from accumulated structure:
- oversized system prompt assembly
- too many skills injected into prompt
- verbose skill descriptions or persistent skill snapshots
- heavy bootstrap / project context files
- long-lived direct sessions carrying too much history
- memory designs that force too much text into every run
- weak cache stability across repeated workflows

This skill should diagnose first, then recommend the smallest safe change that reduces prompt weight.

## What this skill should inspect

### 1) Session-level signals

Check the sessions that matter most:
- fresh direct sessions
- long-lived direct sessions
- recurring heartbeat sessions
- recurring cron sessions

Look for:
- context already large near the start of a session
- low cache hit rate on repeated workflows
- short tasks consuming surprisingly high tokens
- large differences between similar agents

### 2) Skill-layer design

Inspect how skills are being loaded.

Common problems:
- broad default skill sets inherited by every agent
- skills that are useful in theory but rarely used in practice
- long skill descriptions bloating prompt text
- agents carrying the same general-purpose skills even when their role is narrow

Recommended direction:
- keep a small `agents.defaults.skills`
- give each agent an explicit, minimal allowlist when possible
- prefer role-specific skills over giant shared bundles
- treat skill count as a prompt budget, not a convenience bucket

### 3) Memory and context layering

Inspect whether the memory design itself is too expensive.

A good pattern is layered memory:
- **L0**: short summary/index loaded often
- **L1**: topic files loaded on demand
- **L2**: daily logs or detailed records loaded only when needed

Watch for these anti-patterns:
- one giant memory file loaded into every session
- detailed logs treated like default context
- repeating the same operational guidance in multiple places
- project files that have grown from useful notes into permanent prompt tax

Recommended direction:
- keep always-loaded memory/index small
- move detail to topic files or dated logs
- load detailed memory only when the task actually needs it
- keep bootstrap/project context on a strict size budget

### 4) Prompt assembly assumptions

If prompt weight is still too high, check the broader assembly path:
- direct sessions defaulting to heavier prompt modes
- project context files injected raw
- safety/tooling sections larger than needed for the workflow
- old sessions preserving prompt state even after config improvements

## Recommended workflow

### Step 1: collect baseline evidence

Capture representative before metrics from comparable workflows:
- fresh direct session vs fresh direct session
- heartbeat run vs heartbeat run
- cron run vs cron run

Primary metrics:
- total tokens
- context size
- cache hit rate
- estimated cost when available

### Step 2: identify the biggest source of waste

Decide whether the dominant cost is coming from:
- session history
- skills injection
- memory layering
- project/bootstrap context
- poor cache reuse
- some combination of the above

### Step 3: apply low-risk slimming

Prefer config changes before source changes.

Typical changes:
- lower `skills.limits.maxSkillsInPrompt`
- lower `skills.limits.maxSkillsPromptChars`
- lower `skills.limits.maxSkillsLoadedPerSource`
- define a smaller `agents.defaults.skills`
- override agents with narrower `skills` lists
- shrink always-loaded memory and move detail to layered memory files
- reduce bootstrap/context file size limits

### Step 4: verify on fresh runs

Do not claim success from old sessions carrying old prompt state.

Always call out when:
- restart is required
- new session is required
- old sessions will remain heavy until replaced

## Safety rules

- Prefer low-risk config changes first.
- Do not remove skills an agent genuinely depends on.
- Do not treat old-session improvements as proof of new baseline efficiency.
- Be explicit about uncertainty when comparing non-identical workflows.

## Good output format

Return 2 to 3 representative before/after datapoints with:
- workflow type
- before metrics
- after metrics
- percent or point change
- one plain-English takeaway

## Example recommendations

- "This agent inherits a broad default skill set but only uses summarize and github in practice. Narrow its allowlist."
- "This system loads detailed memory by default. Split it into layered memory so only the small index is always loaded."
- "This direct session is heavy before any real work begins, so the waste is likely in prompt assembly rather than task complexity."
- "Cache hit is healthy, but the stable prefix is still too large. Shrink the prompt inputs themselves."
