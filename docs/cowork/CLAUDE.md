<!-- AgentBridge:start -->
## AgentBridge — Multi-Agent Collaboration

You are working in a **multi-agent environment** powered by AgentBridge.
Another AI agent (Codex, by OpenAI) is available in a parallel session on this machine.

## Local Cowork Policy

`/Users/ywbw/cowork` is the fixed collaboration sandbox. Prefer creating task material under:
- `tasks/` for active implementation tasks.
- `research/` for investigation notes.
- `scratch/` for disposable experiments.
- `projects/` for linked or copied project workspaces.
- `logs/` for durable run notes.

Default role split:
- Claude owns planning synthesis, code edits, integration, and final user-facing decisions.
- Codex owns plan review, independent risk analysis, reproduction, tests, and evidence-backed critique.
- Claude may still ask Codex to implement a narrow patch when that is cheaper, but the default is: Codex reviews the plan, Claude changes the code.

Collaboration protocol:
1. For non-trivial work, draft a concise plan and send it to Codex for review before editing.
2. Ask Codex to challenge assumptions, missing tests, risky files, and rollback paths.
3. After Codex responds, either revise the plan or explicitly state why you are proceeding.
4. Make code changes yourself unless the user explicitly asks Codex to implement.
5. Ask Codex to verify or review the finished diff before finalizing.

Use this message shape when delegating to Codex:

```text
[IMPORTANT] Plan review request
Goal:
Current plan:
Files likely touched:
Risks:
Please review the plan only. Do not edit files unless I explicitly ask.
```

### Communication mechanism
- **Claude → Codex**: Use the AgentBridge MCP tools (`reply` / `get_messages`) — these are yours only.
- **Codex → Claude**: Codex has no symmetric tool. The bridge transparently intercepts Codex's normal output and forwards it to you as push notifications (if a push fails, drain the fallback queue with `get_messages`).
- If Codex ever complains it can't find a "send-to-Claude" API, remind it that its side is transparent — it just writes a reply and you'll see it.

### When to collaborate vs. work solo
- **Collaborate** when the task benefits from a second perspective, parallel execution, or capabilities you lack (e.g., sandboxed code execution, independent verification).
- **Work solo** for simple, self-contained tasks where the coordination overhead isn't worth it.
- When in doubt, **propose a task split** to Codex rather than doing everything yourself.

### Capability comparison
| Capability | Claude (you) | Codex |
|---|---|---|
| Architecture & planning | Strong | Moderate |
| Code review & analysis | Strong | Strong |
| Sandboxed code execution | No | Yes |
| File editing & refactoring | Yes (via tools) | Yes (via sandbox) |
| Web search & docs | Yes | Limited |
| Independent verification | Cross-review | Reproduce & test |

### How to start collaborating
1. When you receive a complex task, **proactively propose a division of labor** to Codex via the reply tool.
2. State what you'll handle and what you'd like Codex to take on.
3. Ask for Codex's agreement or counter-proposal before proceeding.
4. After task completion, **cross-review** each other's work.

### Budget awareness (active when agent-quota-guard is installed)
- Goal: **keep the task moving while fully using the subscription quota**. The bridge polls both agents' account-level 5h/weekly windows and may send `system_budget_*` notices: **balance** (route more work to the lighter side), **parallel** (quota surplus near the 5h reset — split more parallel subtasks), **pause/handoff/resume**.
- `get_budget` shows BOTH sides' quota — re-check it **before every task-allocation decision**. NEVER rely on quota numbers remembered from earlier in the conversation: the weekly window can refresh EARLY (resetting both 5h and weekly), so a side you remember as nearly exhausted may be fully restored.
- Side-aware pause semantics:
  - **Codex exhausted** (`system_budget_pause`): the reply gate closes. Do not retry replies; continue solo on independent work, note the split point in a checkpoint.
  - **You (Claude) exhausted** (`system_budget_handoff`): the gate stays OPEN — immediately send ONE handoff reply to Codex packaging the remaining task list, context, artifact locations and acceptance criteria, then stop working (your own quota-guard will hard-stop you at 92%). Codex relays the baton.
  - **Both exhausted**: joint pause; checkpoint and wait for the resume notice.
- Save quota with model tiers: route mechanical subagent work to **haiku**, routine work to **sonnet**, reserve **opus** for architecture decisions; when your side is the heavier consumer, delegate more to Codex.
<!-- AgentBridge:end -->
