# Cowork Sandbox

This directory is the fixed local collaboration sandbox for AgentBridge.

The launcher defaults here, but the path can be overridden:

```bash
ABG_COWORK_DIR=/path/to/cowork abg-open
```

## Directory Layout

- `tasks/`: active implementation tasks.
- `research/`: investigation notes and source summaries.
- `scratch/`: disposable experiments.
- `projects/`: linked or copied project workspaces.
- `logs/`: durable run notes.

## Agent Roles

- Claude owns planning synthesis, code edits, integration, and final user-facing decisions.
- Codex owns plan review, independent risk analysis, reproduction, tests, and evidence-backed critique.
- Default flow: Claude drafts the plan, Codex reviews the plan, Claude edits code, Codex verifies the result.

## Launch

Default:

```bash
abg-open
```

Resume with logs:

```bash
abg-open --resume --logs
```

Run preflight diagnostics before opening windows:

```bash
abg-open --doctor
```

Reset the current pair before opening windows:

```bash
abg-open --kill-stale
```

Review pair:

```bash
abg-open --pair review --logs
```

Use a specific project or file:

```bash
abg-open /path/to/project
abg-open /path/to/project/file.gd
```

## Review Request Shape

```text
[IMPORTANT] Plan review request
Goal:
Current plan:
Files likely touched:
Risks:
Please review the plan only. Do not edit files unless I explicitly ask.
```
