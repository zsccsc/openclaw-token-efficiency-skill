# Publishing Guide

## Recommended repository name

`openclaw-token-efficiency-skill`

## Minimal publish flow

1. Create a new GitHub repository.
2. Copy these files into the repo root:
   - `SKILL.md`
   - `README.md`
   - `LICENSE`
3. Update the `homepage` field in `SKILL.md` to your real GitHub URL.
4. Push the repository.
5. Optionally add tags and a release.

## Example commands

```bash
mkdir openclaw-token-efficiency-skill
cd openclaw-token-efficiency-skill
cp /path/to/SKILL.md .
cp /path/to/README.md .
cp /path/to/LICENSE .
git init
git add .
git commit -m "Initial public skill release"
```

## Optional nice-to-haves

- Add a screenshot or status card example to README
- Add a changelog
- Add example before/after measurements
- Submit to a community skill index if you want discovery

## Suggested positioning

Short version:

> A public OpenClaw skill for auditing token waste, shrinking prompt overhead, and validating before/after efficiency gains.

Longer version:

> This skill helps OpenClaw agents diagnose prompt bloat and token inefficiency caused by oversized skill injection, heavy bootstrap context, and poor cache reuse. It recommends low-risk config changes and verifies results with real before/after measurements.
