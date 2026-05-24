# baryon-todos plugin for Claude Code

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Plugin-blue)](https://docs.anthropic.com/en/docs/claude-code/overview)

A Claude Code plugin that bundles the **`/baryon-todos` skill** + auto-registered
**MCP server** for the Baryon H-remote claim tracker
(https://baryon-todo-dashboard.fly.dev).

Inspired by trello-mcp: ask Claude to list active pool, create new dynamic
tasks, inspect who worked on what, override task records — all from any
Claude Code session.

## What's inside

```
baryon-todos-plugin/
├── .claude-plugin/
│   ├── plugin.json          # plugin manifest
│   └── marketplace.json     # marketplace entry
├── .mcp.json                # auto-launches @baryonlabs/baryon-todos-mcp
└── skills/
    └── baryon-todos/
        └── SKILL.md         # /baryon-todos skill definition
```

The MCP server is published separately as
[`@baryonlabs/baryon-todos-mcp`](https://github.com/baryonlabs/baryon-todos-mcp).
The plugin pulls it via `npx -y @baryonlabs/baryon-todos-mcp` so users don't
need to clone the MCP repo manually.

## Install

### Option 1 — Marketplace (recommended)

```bash
claude plugin marketplace add https://github.com/baryonlabs/baryon-todos-plugin
claude plugin install baryon-todos@baryon-todos
```

### Option 2 — Local checkout

```bash
git clone https://github.com/baryonlabs/baryon-todos-plugin.git ~/.claude/plugins/marketplaces/baryon-todos
claude plugin marketplace refresh
claude plugin install baryon-todos@baryon-todos
```

## Configure environment

The MCP server needs basic-auth credentials for the upstream tracker. Set
these in your shell or in your Claude Code env config:

```bash
export TRACKER_BASE_URL=https://baryon-todo-dashboard.fly.dev
export TRACKER_USER=tracker
export TRACKER_PASSWORD=<your-basic-auth-password>
```

Or pass them at plugin install time via `--env`.

## Use

Trigger the skill in any Claude Code session:

```
/baryon-todos 지금 활성 풀 보여줘
/baryon-todos 새 H-remote 업무 등록 - "알리고 1544-0001 전화", 우선순위 1
/baryon-todos /task/dyn/<uuid> 이력 보여줘
```

The skill routes to one of the 11 MCP tools (`mcp__baryon-todos__*`):

| Tool | Purpose |
|---|---|
| `list_active_pool` | Active claims + first-seen timestamps |
| `list_dynamic_tasks` | Admin-created dynamic tasks |
| `get_dynamic_task` | Lookup one dynamic task |
| `create_dynamic_task` | Add new pool task |
| `update_dynamic_task` | Edit task |
| `archive_dynamic_task` | Soft-delete task |
| `get_task_history` | Per-tid claim history + snapshots |
| `get_task_record` | Current record |
| `save_task_record` | Admin override write |
| `list_task_messages` | Chat messages |
| `list_naver_roles` | Naver role assignments (superadmin) |

## License

MIT © Baryon AI Laboratory
