# StarChain Plugin API v2.9 (Lightweight Contract)

## Goal

Define a lightweight plugin contract for StarChain so plugins can be reasoned about consistently without over-engineering a formal runtime.

This is a documentation-level API, not a code runtime API.

## Plugin object model

Each plugin should declare these conceptual fields in its documentation or orchestration usage:

- `name`
- `role`
- `phase`
- `defaultMode`
- `triggers`
- `inputs`
- `outputs`
- `escalatesWhen`
- `handsOffTo`

## Field meanings

### `name`
The plugin identifier.

### `role`
What the plugin is for.
Examples:
- product framing
- lite planning
- quality gate
- browser QA
- retro
- guardrail
- design sanity review
- root-cause investigation

### `phase`
Where the plugin belongs in StarChain.
Suggested values:
- pre-plan
- lite-plan
- pre-build
- post-build
- qa
- post-delivery
- incident

### `defaultMode`
How the plugin normally behaves.
Suggested values:
- core
- optional
- conditional
- escalation-only

### `triggers`
What conditions should cause the plugin to be used.
Use operational language rather than vague product language.

### `inputs`
What context the plugin expects.
Examples:
- task statement
- plan draft
- changed scope
- failing workflow
- reviewed implementation

### `outputs`
What structured result the plugin must return.
Examples:
- wedge decision
- execution plan
- gate verdict
- QA note
- retro summary
- guard profile
- candidate root causes

### `escalatesWhen`
When the plugin should request expansion to a heavier route or broader scope.

### `handsOffTo`
What next StarChain node or plugin should usually follow.

## Canonical plugin map

### founder-office-hours
- role: product framing
- phase: pre-plan
- defaultMode: conditional for L2, core for L3
- handsOffTo: autoplan-lite or Constitution-First

### autoplan-lite
- role: lite planning core
- phase: lite-plan
- defaultMode: core for L2
- handsOffTo: coding or Full escalation

### review-gate
- role: quality gate
- phase: post-build
- defaultMode: core
- handsOffTo: test, qa-browser-check, or coding rework

### qa-browser-check
- role: browser QA
- phase: qa
- defaultMode: conditional
- handsOffTo: docs or coding rework

### release-retro
- role: post-delivery learning
- phase: post-delivery
- defaultMode: conditional
- handsOffTo: memory, .learnings, checklist, plugin improvement

### guard-mode
- role: scoped execution guardrail
- phase: pre-build / incident
- defaultMode: conditional
- handsOffTo: coding, review, investigate-root-cause

### design-review-lite
- role: product/design sanity review
- phase: pre-build
- defaultMode: conditional
- handsOffTo: autoplan-lite, coding, qa-browser-check

### investigate-root-cause
- role: diagnosis
- phase: incident / pre-build
- defaultMode: conditional
- handsOffTo: guard-mode, coding, review-gate

## Design rules

- Prefer a lightweight documented contract over a formal plugin runtime until real usage proves otherwise.
- Keep plugin outputs structured enough for main to route the next step.
- Avoid plugins that do not clearly change route, scope, verdict, or retention quality.
- Treat plugins as orchestration primitives, not decorative labels.

## Future evolution trigger

Only build a stronger runtime/plugin manifest system if:
- plugin count grows beyond what SKILL.md + references can manage cleanly
- route selection becomes error-prone without machine-readable metadata
- multiple plugins need shared fields enforced across the suite
