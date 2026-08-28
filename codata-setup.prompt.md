---
description: "Set up Codata as your project's backend: workspace creation, generation, and SDK integration"
mode: "agent"
tools: ["mcp_codata_workspaces-list", "mcp_codata_workspaces-create", "mcp_codata_workspaces-get", "mcp_codata_conversations-sendMessage", "mcp_codata_conversations-getMessageStatus", "mcp_codata_plan_backend_change", "mcp_codata_apply_backend_plan", "mcp_codata_get_task", "mcp_codata_workspaces-createApiKey", "mcp_codata_workspaces-getApiUsage", "mcp_codata_workspaces-validateIntegration", "mcp_codata_organizations-list"]
---

# Set Up Codata Backend

You are helping the user set up **Codata** as their project's backend. Describe what you want to build through conversation, and Codata builds and deploys the full backend (database, API endpoints, SDK) automatically.

## Prerequisites

The user must have the **Codata MCP server** connected. If tool calls fail with "not found", tell the user to add Codata MCP to their VS Code settings:
```json
"mcp": { "servers": { "codata": { "url": "https://mcp.codata.io/api/mcp" } } }
```

## Workflow — Follow These Steps IN ORDER

1. **organizations-list** — pick context (personal or org).
2. **workspaces-list** — pick or create a workspace.
3. **workspaces-get** — note onboarding.conversationId. Always reuse it; never create new conversations.
4. **conversations-sendMessage** — drive the spec through PURPOSE → ASSETS → ENDPOINTS → READY. Poll **conversations-getMessageStatus** every 3-5s until each turn completes.
5. **plan_backend_change** — hand over the whole intent and review the returned plan (diff, risk, approvals, open questions; relay product questions to the user). Then **apply_backend_plan** (the only tool that deploys) + poll **get_task** until the task completes.
6. **workspaces-createApiKey**, **workspaces-getApiUsage** (language: "sdk-typescript"), `npm install codata-io`, write all files, update .gitignore (.env, .env.local, .codata/), then **workspaces-validateIntegration**.

## Rules

- Use the `codata-io` SDK (`ws.get()`, `ws.post()`) — never raw fetch/axios.
- Use types from `codata.types.ts` — never define separate interfaces or Zod schemas.
- No ORMs (Prisma, Mongoose, etc.) — Codata IS the backend.
- No mappers/transformers — pass SDK responses directly.
