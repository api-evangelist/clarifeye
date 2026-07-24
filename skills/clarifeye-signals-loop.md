---
name: Submit and triage knowledge signals
description: Raise a signal about a Clarifeye knowledge store's content, list signals for review, and update a signal's status.
api: openapi/clarifeye-openapi-original.yml
operations: [submitSignal, listSignals, updateSignal]
---

# Submit and triage knowledge signals

Signals are the feedback loop: when an answer is wrong or missing, raise a signal so
domain experts can review it.

## Auth
`Authorization: Bearer <token>` (or `Token <token>`); base
`https://{eu|us}.app.clarifeye.ai/api/v1`; paths scoped to `/projects/{project_id}/`.

## Steps
1. **Submit** — `submitSignal` (`POST /projects/{project_id}/signals/`) with the signal
   text plus optional `context.additional_details`. It appears in the review UI for
   domain experts.
2. **List** — `listSignals` (`GET /projects/{project_id}/signals/`), optionally filtered
   by `status`. Page-number pagination.
3. **Resolve** — `updateSignal` (`PATCH /projects/{project_id}/signals/{signal_id}/`) to
   mark a signal reviewed or update fields.

## Rules
- Errors return `{"error": "..."}`; 403 indicates the caller lacks review permission.
- Agents connected via MCP raise the same signals through `create_feedback` and triage
  through `list_feedback` / `resolve_feedback` (mcp/clarifeye-mcp.yml).
