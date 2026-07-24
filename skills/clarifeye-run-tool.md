---
name: Discover and run a knowledge-store tool
description: List the tools a Clarifeye knowledge store exposes, read a tool's parameter schema, and execute it.
api: openapi/clarifeye-openapi-original.yml
operations: [listTools, getToolParameters, runTool]
---

# Discover and run a knowledge-store tool

## Auth
`Authorization: Bearer <token>` (or `Token <token>`); base
`https://{eu|us}.app.clarifeye.ai/api/v1`; paths scoped to `/projects/{project_id}/`.

## Steps
1. **List** — `listTools` (`GET /projects/{project_id}/tools/`) returns tools owned by
   the project (`category: local`) and imported ones (`category: imported`). Note the
   `id` of the tool you want.
2. **Read schema** — `getToolParameters`
   (`GET /projects/{project_id}/tools/{tool_id}/parameters/`) returns the JSON Schema of
   the parameters `runTool` accepts. Build the request body from it.
3. **Run** — `runTool` (`POST /projects/{project_id}/tools/{tool_id}/run/`) with the
   validated parameters. On success you get a `ToolRunResponse`.

## Rules
- On tool-execution failure the response is a structured `ToolError`
  (`error_type`, `error_code`, `status`, `message`) — e.g. `syntax_error` / `INVALID_QUERY`.
- A 404 means the tool or its index was not found; 401/403 are auth/permission failures.
- The same catalogue and flow are exposed to AI clients via the MCP tools
  `list_tools` / `get_tool` / `run_tool` (mcp/clarifeye-mcp.yml).
