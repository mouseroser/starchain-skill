---
name: guard-mode
description: Add task-local execution guardrails inside StarChain. Use when a coding, review, debugging, production, or repo-change task needs temporary safety constraints such as allowed directories, read-only review mode, config freeze, destructive-command warning, or restricted scope editing. Best for large repos, production troubleshooting, sensitive config areas, and ACP/coding sessions where bounded action matters more than speed.
---

# Guard Mode

Use this plugin to impose **temporary execution boundaries** on a StarChain run.

The goal is not to replace system safety rules. The goal is to add task-local guardrails so the run stays inside the intended blast radius.

## Good fits

Use when the task needs one or more of these constraints:
- only edit a specific directory
- read-only review mode
- do not touch config
- do not touch deployment/runtime files
- warn before destructive commands
- freeze scope to a small subsystem
- production debugging where observation is allowed but mutation is tightly bounded

## Output contract

Produce a short guard profile with these sections:
- Allowed scope
- Forbidden scope
- Edit policy
- Command policy
- Escalation triggers
- Exit condition

## Guard profiles

### Directory-bound edit mode
Use when only one app/module/path may be changed.

Define:
- allowed directories
- forbidden directories
- whether file creation is allowed
- whether rename/delete is allowed

### Read-only review mode
Use when the task is analysis/review only.

Define:
- no file edits
- no write commands
- no config mutation
- output must be findings only

### Config freeze mode
Use when code changes are allowed but config changes are not.

Define:
- config paths frozen
- schema or runtime settings frozen
- env/secret/config files off limits

### Destructive-warning mode
Use when cleanup, migrations, resets, or deletes are in scope.

Define:
- warn before destructive actions
- prefer non-destructive inspection first
- require an explicit go/no-go checkpoint before the destructive step

## Default policy language

State guardrails clearly and operationally:
- only edit `X`
- do not modify `Y`
- treat `Z` as read-only
- warn before delete/reset/migrate operations
- stop and escalate if the task expands beyond the allowed scope

## Escalation rules

Escalate or pause when:
- the root cause is outside the allowed scope
- a forbidden directory must change to finish the task
- the task changes from review to mutation
- a destructive action becomes necessary
- the requested scope is internally inconsistent

## Handoff guidance

When used inside StarChain:
- apply `guard-mode` before coding or investigation begins
- include the guard profile in the spawned task prompt
- keep the profile short and concrete
- if the guard profile blocks completion, return the smallest necessary scope expansion request

## Example triggers

- “这次只准改 apps/web”
- “先只读审查，不要动代码”
- “不要碰配置和部署，只看业务逻辑”
- “这次排障先观察，危险命令先提示”
