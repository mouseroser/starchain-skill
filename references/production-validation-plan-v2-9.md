# StarChain v2.9 Production Validation Plan

## Goal

Validate the StarChain v2.9 plugin suite in real production-like usage, not just by packaging or document review.

The suite under validation:
- founder-office-hours
- autoplan-lite
- review-gate
- qa-browser-check
- release-retro
- guard-mode
- design-review-lite
- investigate-root-cause

## Validation Questions

1. Does the correct plugin trigger at the correct task weight?
2. Does Lite reduce over-orchestration on L2 work?
3. Do review-gate verdicts improve handoff quality?
4. Does qa-browser-check catch browser-visible failures that code/test review misses?
5. Does release-retro produce reusable improvements rather than ceremony?
6. Do second-batch plugins solve real repeated pain or just look elegant on paper?

## Validation Matrix

### Track A — L2 feature planning
Use a real medium-complexity task.

Expected path:
- founder-office-hours (optional)
- autoplan-lite
- coding (optional downstream if implementation follows)

Success indicators:
- scope gets meaningfully reduced or clarified
- ordered execution plan is directly usable
- no need to escalate to Full unless genuine uncertainty appears

### Track B — UI / workflow change
Use a real browser-visible task.

Expected path:
- autoplan-lite or Full
- coding
- review-gate
- qa-browser-check

Success indicators:
- review-gate produces a clear go/no-go verdict
- qa-browser-check validates the main user path and catches at least one meaningful issue or confirms usability cleanly

### Track C — recurring bug / incident
Use a real failure or regression.

Expected path:
- investigate-root-cause
- guard-mode (if scope/risk needs bounding)
- coding
- review-gate

Success indicators:
- investigation narrows fault domain before edits
- fix targets mechanism rather than symptoms
- fewer blind edits

### Track D — completed delivery with lessons
Use a real L2/L3 delivery after completion.

Expected path:
- release-retro

Success indicators:
- retro creates at least one reusable memory, learning, checklist update, or plugin improvement candidate
- retro is short and operational, not ceremonial

## Evaluation Rubric

Score each run from 1-5 on:
- route selection quality
- usefulness of plugin output
- reduction of unnecessary work
- handoff clarity
- correctness / stability improvement
- long-term retention value

## Minimum validation threshold

Treat v2.9 pluginization as production-proven only if:
- at least 3 real runs across 2+ task types succeed
- at least 1 browser-visible task validates qa-browser-check
- at least 1 diagnosis task validates investigate-root-cause or guard-mode
- at least 1 retro yields durable improvement output

## Data to capture after each run

Capture:
- task type
- route chosen
- plugins used
- whether escalation happened
- what plugin changed the outcome materially
- what should be tightened next

## Next-step decision rules

After the first 3-5 real runs:
- keep plugins that materially change outcomes
- downgrade or archive plugins that add ceremony without leverage
- refine route selection rules using observed misfires
