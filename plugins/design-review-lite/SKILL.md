---
name: design-review-lite
description: Run a lightweight product/design review inside StarChain before or alongside implementation planning. Use when a feature, UI flow, dashboard, settings page, information architecture, or interaction model needs a quick design sanity check for clarity, user friction, scope, consistency, and missing states without invoking a full design process.
---

# Design Review Lite

Use this plugin to run a compact product/design sanity check.

This is not a full design system review and not a high-fidelity UX process. It is a fast way to catch obvious interaction, clarity, flow, and state problems before implementation goes too far.

## Good fits

Use when the task involves:
- UI screens
- multi-step user flows
- settings or dashboard information architecture
- forms and validation UX
- feature surfaces that may confuse users/operators
- product scope that is functionally correct but may be awkward to use

## Output contract

Produce a compact review with these sections:
- Core user task
- Main clarity risks
- Flow friction points
- Missing states / edge cases
- Recommended simplifications
- Design verdict

## Review lens

Check for:
- user goal clarity
- unnecessary steps
- confusing labels or structure
- missing empty/loading/error states
- overexposed options in v1
- mismatch between business logic and user mental model

## Default verdicts

Use one of:
- Good enough for build
- Build but simplify first
- Needs flow revision before build
- Blocked on product decision

## Workflow

### Step 1 — Restate the user task

Describe what the user/operator is actually trying to achieve.

### Step 2 — Walk the main flow

Ask:
- what is the shortest path to success?
- where could the user hesitate or misread?
- what feels heavier than necessary?

### Step 3 — Check state coverage

Look for missing:
- empty states
- loading states
- validation states
- failure states
- permission or blocked states

### Step 4 — Simplify aggressively

Prefer fewer:
- controls
- branches
- fields
- pages
- choices

### Step 5 — Return a design verdict

End with a direct recommendation the main orchestrator can use.

## Handoff guidance

Use this plugin:
- before coding for UI-heavy work
- after `founder-office-hours` if product framing is solid but the interface shape still feels fuzzy
- before `qa-browser-check` if the flow needs a structural sanity pass first

## Example triggers

- “先帮我快速过一下这个页面/流程设计”
- “这个功能逻辑没问题，但交互可能还不顺”
- “别上完整设计流程，先做一轮轻量设计评审”
