# Local Cowork Workflow

This is a personal workflow layer for running AgentBridge with a fixed local
collaboration sandbox and a predictable Claude/Codex role split.

It does not change AgentBridge core behavior. It adds:

- `scripts/abg-open`: a macOS Terminal launcher for paired Claude + Codex windows.
- `docs/cowork/`: templates for the fixed cowork sandbox.

## Reviewed Local Design

The local workflow was reviewed for the following risks:

- Repeatedly launching from random directories can cross-wire pair state.
- `abg resume` can use AgentBridge's max-permission defaults unless safe mode is
  explicitly carried through.
- A hard-coded cowork path is convenient locally but should remain overrideable.
- Claude and Codex need a stable division of labor to avoid duplicate edits or
  low-value back-and-forth.

The current version addresses those points:

- Default workspace is `/Users/ywbw/cowork`.
- `ABG_COWORK_DIR` can override that default.
- `--safe` is the default for normal launches.
- `--resume` also sets `AGENTBRIDGE_SAFE=1` unless `--unsafe` is requested.
- Claude owns planning synthesis, code edits, integration, and final response.
- Codex owns plan review, independent risk analysis, reproduction, tests, and
  evidence-backed verification.

## Install Locally

From this repository:

```bash
chmod +x scripts/abg-open
ln -sf "$PWD/scripts/abg-open" /opt/homebrew/bin/abg-open
```

Create the cowork directory and copy the templates:

```bash
mkdir -p /Users/ywbw/cowork
cp docs/cowork/README.md /Users/ywbw/cowork/README.md
cp docs/cowork/CLAUDE.md /Users/ywbw/cowork/CLAUDE.md
cp docs/cowork/AGENTS.md /Users/ywbw/cowork/AGENTS.md
mkdir -p /Users/ywbw/cowork/tasks \
  /Users/ywbw/cowork/research \
  /Users/ywbw/cowork/scratch \
  /Users/ywbw/cowork/projects \
  /Users/ywbw/cowork/logs
```

Then initialize AgentBridge there if needed:

```bash
cd /Users/ywbw/cowork
abg init
```

## Usage

Open the default cowork pair:

```bash
abg-open
```

Resume the existing Claude/Codex pair and open logs:

```bash
abg-open --resume --logs
```

Run diagnostics before opening windows:

```bash
abg-open --doctor
```

Kill the current pair before opening:

```bash
abg-open --kill-stale
```

Start a named pair:

```bash
abg-open --pair review --logs
```

Use another workspace:

```bash
abg-open /path/to/project
ABG_COWORK_DIR=/path/to/cowork abg-open
```

## Default Collaboration Contract

Claude should send non-trivial plans to Codex before editing:

```text
[IMPORTANT] Plan review request
Goal:
Current plan:
Files likely touched:
Risks:
Please review the plan only. Do not edit files unless I explicitly ask.
```

Codex should reply with a direct verdict:

```text
[IMPORTANT] Plan review
Verdict:
Required changes:
Risks:
Suggested verification:
```

## Known Boundaries

- This is a local macOS Terminal workflow. It uses `osascript`.
- It assumes `abg`, `claude`, and `codex` are already installed and available in
  PATH.
- For Codex installed through the desktop app, expose the bundled binary if
  AgentBridge cannot find `codex`:

```bash
ln -s /Applications/Codex.app/Contents/Resources/codex /opt/homebrew/bin/codex
```

- Keep `--safe` as the default for real projects. Use `--unsafe` only in
  disposable workspaces.
