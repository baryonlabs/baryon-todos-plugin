---
name: baryon-todos
description: "Baryon H-remote claim tracker - list pool, create dynamic tasks, inspect claim history, save task records via MCP server"
trigger: /baryon-todos
---

# /baryon-todos

Talk to the Baryon H-remote claim tracker (https://baryon-todo-dashboard.fly.dev) through the `baryon-todos` MCP server.

Use this skill when the user asks to:
- list currently claimed/active H-remote tasks
- create a new H-remote task for freelancers to pick up
- review who worked on which task (claim history with result/evidence snapshots)
- look up or override a task record (customDesc / result / evidence / confirmation)
- inspect task chat messages

## Setup (one-time)

Install the MCP server from https://github.com/baryonlabs/baryon-todos-mcp:

```bash
git clone https://github.com/baryonlabs/baryon-todos-mcp.git
cd baryon-todos-mcp && npm install && npm run build
```

Register in your MCP client (`claude_desktop_config.json` or via the plugin):

```json
{
  "mcpServers": {
    "baryon-todos": {
      "command": "node",
      "args": ["/abs/path/baryon-todos-mcp/dist/index.js"],
      "env": {
        "TRACKER_BASE_URL": "https://baryon-todo-dashboard.fly.dev",
        "TRACKER_USER": "tracker",
        "TRACKER_PASSWORD": "<basic-auth>"
      }
    }
  }
}
```

## Available tools (prefixed `mcp__baryon-todos__`)

| Tool | Use when |
|---|---|
| `list_active_pool` | "어떤 업무가 진행 중이야?" |
| `list_dynamic_tasks` | "관리자가 등록한 업무 목록" |
| `create_dynamic_task` | "새 H-remote 업무 등록해줘" — args: title (req), project, category, priority(1-4), duration_label, description |
| `update_dynamic_task` | "이 동적 업무 수정" — args: id (req) + fields |
| `archive_dynamic_task` | "이 동적 업무 삭제(보관)" — args: id |
| `get_task_history` | "이 업무 누가 했어?" — args: tid `/task/...` |
| `get_task_record` | "이 업무 결과/증빙 보여줘" — args: tid |
| `save_task_record` | "이 업무 결과 admin이 수정" — args: tid + evidence/result/customDesc |
| `list_task_messages` | "이 업무 채팅 보여줘" — args: tid |
| `list_user_roles` | "사용자 role 목록" (superadmin 필요) |

## TID format

- Hardcoded role-board task: `/task/{col}/{project-slug}/{title-slug}` (col = `alba` / `alba-tel` / `alba-contact` / `alba-auth`)
- Dynamic task: `/task/dyn/{uuid}`

## Conventions

- Priority: 1=최우선, 2=긴급, 3=다음, 4=일반
- Category keys: `phone`, `email`, `doc`, `register`, `research`, `manage`, `visit`, `content`, `app`, `web`, `review`, `expert`, `general`
- Claim TTL: 1시간. 완료/취소 시 `result_snapshot` + `evidence_snapshot`이 claim row에 보존

## Examples

```
/baryon-todos 지금 활성 풀 보여줘
/baryon-todos 새 업무 등록 - 알리고 1544-0001 전화, 우선순위 최우선, 30분
/baryon-todos /task/dyn/abc-123 이력
```
