---
name: ensue-memory
description: Persistent memory layer for AI agents via Ensue Memory Network API. Use when users ask to remember, recall, search memories, manage permissions, or subscribe to updates. Triggers on "remember this", "recall", "search memories", "update memory", "list keys", "share", "subscribe to", "permissions", or any persistent storage request.
---

# Ensue Memory Network

Dynamic memory service accessed via curl.

## IMPORTANT: Do NOT use native MCP tools

**Do NOT use:**
- `listMcpResources`
- `mcp__ensue-memory-local__*` tools
- Any native MCP tool calls

**ONLY use curl** as described below. This ensures consistent behavior and dynamic schema discovery.

## Execution Order (MUST FOLLOW)

**Step 1: Check for API key**

```bash
claude mcp get memory-network-ensue
```

Extract the Bearer token from the headers. If not found, stop and tell user:
> "Ensue Memory Network is not configured. To set up:
> 1. Get an API key from https://www.ensue-network.ai/dashboard
> 2. Run: `claude mcp add memory-network-ensue https://api.ensue-network.ai/ --header \"Authorization: Bearer YOUR_API_KEY\"`"

**Step 2: List available tools (REQUIRED before any tool call)**

```bash
curl -X POST https://api.ensue-network.ai/ \
  -H "Authorization: Bearer <API_KEY>" \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"tools/list","id":1}'
```

This returns tool names, descriptions, and input schemas. **Never skip this step.**

**Step 3: Call the appropriate tool**

```bash
curl -X POST https://api.ensue-network.ai/ \
  -H "Authorization: Bearer <API_KEY>" \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"tools/call","params":{"name":"<tool_name>","arguments":{<args>}},"id":1}'
```

Use the schema from Step 2 to construct correct arguments.

## Intent Mapping

| User says | Tool to call |
|-----------|--------------|
| "remember...", "save...", "store..." | create_memory |
| "what was...", "recall...", "get..." | get_memory or search_memories |
| "search for...", "find..." | search_memories |
| "update...", "change..." | update_memory |
| "delete...", "remove..." | delete_memory |
| "list keys", "show memories" | list_keys |
| "share with...", "give access..." | share |
| "who can access...", "permissions" | list_permissions |
| "notify when...", "subscribe..." | subscribe_to_memory |

## Key Naming

Use hierarchical paths: `category/subcategory/name`

Examples: `preferences/theme`, `project/api-keys`, `notes/meeting-2024-01`
