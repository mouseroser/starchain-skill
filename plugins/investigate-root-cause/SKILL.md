---
name: investigate-root-cause
description: Run a structured root-cause investigation inside StarChain for bugs, regressions, flaky workflows, production incidents, or recurring failures. Use when the task is not just to patch symptoms but to identify the real failure mechanism, narrow the fault domain, test candidate causes, and recommend the smallest durable fix.
---

# Investigate Root Cause

Use this plugin when a bug or failure should be diagnosed systematically instead of patched by guesswork.

The goal is to identify the failure mechanism, not merely make the error disappear once.

## Good fits

Use when dealing with:
- recurring bugs
- regressions after recent changes
- flaky workflows
- production incidents
- failures with multiple plausible causes
- situations where a quick patch may hide the real issue

## Output contract

Produce an investigation brief with these sections:
- Symptom
- Most likely fault domain
- Candidate root causes
- Evidence for / against
- Recommended next probe or fix
- Confidence level

## Investigation workflow

### Step 1 — Define the symptom precisely

State:
- what failed
- where it failed
- what was expected
- what was observed instead

### Step 2 — Bound the fault domain

Narrow the likely area:
- UI
- API
- database
- config
- auth
- browser state
- external dependency
- orchestration / prompt / task contract

### Step 3 — Generate candidate causes

Prefer a short ranked list over a long brainstorming dump.

### Step 4 — Compare evidence

For each candidate, ask:
- what evidence supports it?
- what evidence weakens it?
- what single probe would most reduce uncertainty?

### Step 5 — Recommend smallest durable fix

Do not jump to the biggest rewrite.
Choose the smallest fix that addresses the confirmed mechanism.

## Escalation rules

Escalate when:
- the symptom crosses subsystems
- the logs/evidence are insufficient to rank causes
- a destructive probe would be needed
- the issue keeps reappearing after multiple symptom-level fixes

## Handoff guidance

Use this plugin:
- before coding when the cause is unclear
- before `review-gate` if the implementation is really a diagnosis task
- with `guard-mode` when prod/risky investigation needs a constrained scope
- before `release-retro` if the failure taught something reusable

## Example triggers

- “别先修，先判断根因”
- “这个问题总反复出现，帮我做根因调查”
- “这次不是普通 bugfix，是 production 排障”
- “给我一版候选根因排序，不要拍脑袋改”
