# Transept MCP server

[![Transept MCP connector](https://glama.ai/mcp/connectors/ai.transept/transept/badges/score.svg)](https://glama.ai/mcp/connectors/ai.transept/transept)

[Transept](https://transept.ai) translates documents with glossaries, style guides and translation memory. This remote MCP server lets Claude, ChatGPT, Cursor or any MCP client import documents, start translation runs, approve review gates and read the results back.

- **Endpoint:** `https://app.transept.ai/api/public/v1/mcp` (Streamable HTTP)
- **Sign-in:** OAuth (the first connection opens a browser window to approve access), or an API key sent as a Bearer header for headless agents. `initialize` and `tools/list` answer without sign-in.
- **Registry:** `ai.transept/transept` in the [official MCP Registry](https://registry.modelcontextprotocol.io)
- **Plan:** the API and MCP come with every paid plan, Lite included.

This repository holds the registry entry (`server.json`) and this documentation. The server itself is hosted by Transept; there is nothing to install.

## Connect

**Claude (web and desktop):** Settings → Connectors → Add custom connector, and paste:

```
https://app.transept.ai/api/public/v1/mcp
```

**ChatGPT:** Settings → Connectors (developer mode) → Create, with the same URL.

**Claude Code:**

```bash
claude mcp add --transport http transept https://app.transept.ai/api/public/v1/mcp
```

**Cursor, VS Code and other clients that read `mcp.json`:**

```json
{
  "mcpServers": {
    "transept": { "url": "https://app.transept.ai/api/public/v1/mcp" }
  }
}
```

## Spending is protected

Tools that bill credits (`run_start`, `group_run_start`, `run_gate_approve`) only return an estimate unless called with `confirm: true`, so an assistant shows you the price before anything is spent. Estimating, translation-memory queries and imports are free.

## Tools

- `me_get`: The account this API key belongs to, the key in use, its scopes, and the credit balance available to spend.
- `project_list`: List projects you can file documents into.
- `project_create`: Create a project (pass team_id for a team project; parent_id for a sub-project of a top-level project — same owner, one level deep).
- `document_list`: List documents, newest first.
- `document_get`: Document metadata + its language group.
- `document_create`: Create a document from inline text/markdown/html and fan out target languages.
- `document_update`: Update document metadata (title / category / project filing).
- `document_delete`: Delete ONE language version of a document.
- `document_add_languages`: Add target-language sibling versions to a document (grows its language group).
- `document_content_get`: Read content as json | markdown | html | strings (binary formats: use the REST content endpoint).
- `document_attach_resources`: Attach/detach glossaries + style-guide versions ([] = detach all of that kind).
- `document_resync_source`: Re-parse the document from its STORED source file and re-align blocks (unchanged blocks keep translations).
- `strings_import`: Import a Transept Strings corpus (.tstrings.json shape).
- `strings_reimport`: Re-submit the WHOLE current corpus against an existing master: the server diffs by unit id and answers with {units, versions[], delta_block_ids} — pass delta_block_ids VERBATIM as group_run_start.block_ids to run only the delta.
- `block_update`: Edit a block's SOURCE content (Slate JSON).
- `block_translation_update`: Edit a translation (Slate JSON); it stays active and feeds TM.
- `processing_job_get`: Poll an import job — the id returned by strings_import (or a REST file upload / Figma import).
- `workflow_list`: Saved workflows (ids go into run_start.workflow_id).
- `workflow_template_list`: Built-in templates (ids go into run_start.template_id).
- `run_estimate`: Price a run (free, no side effects).
- `run_start`: Start a workflow run.
- `run_get`: Poll a run: status, progress, and (when gate_pending) the review-gate digest.
- `run_list`: List runs, newest first.
- `run_cancel`: Cancel a run (idempotent — answers with the resulting status, so an already-terminal run reads as what it already was).
- `run_gate_approve`: Approve a review gate.
- `run_gate_acknowledge`: Dismiss a review gate without continuing.
- `group_run_estimate`: Price a multi-language fan-out (free).
- `group_run_start`: Run a workflow across many languages.
- `group_run_get`: Poll a group run: rollup + per-language lanes.
- `group_run_list`: List group runs, newest first.
- `group_run_cancel`: Cancel every still-active lane of a group run.
- `glossary_list`: List glossaries (yours + your teams’).
- `glossary_get`: One glossary with its terms.
- `glossary_create`: Create a glossary (optionally seed terms).
- `glossary_terms_add`: Add terms in bulk; the answer is counts.
- `styleguide_list`: List style guides with their ACTIVE version id (what documents consume).
- `styleguide_get`: One style guide incl.
- `tm_query`: Query translation memory (hybrid retrieval; free).
- `tm_import`: Import a TMX/XLIFF file’s text into translation memory (free; async embeddings).
- `webhook_list`: List outgoing webhook endpoints (no secrets).
- `webhook_create`: Register an outgoing webhook.
- `webhook_delete`: Delete an outgoing webhook endpoint.

## Links

- Website: https://transept.ai
- How to connect an assistant: https://transept.ai/help/connect-ai-assistant
