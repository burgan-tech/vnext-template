# MCP task example (type 24, experimental)

`crm-mcp-task.json` is a `sys-tasks` component that calls one operation of a remote MCP (Model Context
Protocol) server over streamable HTTP. It is kept outside the domain folder on purpose: the runtime
checks the endpoint host against the orchestration host's `Mcp:AllowedHosts` when the task is published
and on every call, and that list is empty (deny all) by default. A fresh project therefore publishes
cleanly until you opt in.

## Use it

1. Copy the file into your domain's `Tasks/` folder and change `key`, `endpoint`, `tool` and `arguments`.
2. Ask the runtime operators to add the endpoint host to `Mcp:AllowedHosts`:

   ```json
   "Mcp": { "AllowedHosts": ["crm-mcp.example.com"] }
   ```

3. Run `npm run validate`, then publish (`wf sync`).

## Credentials

The task never carries a credential. `auth.secretRef` and every `headers` value name a secret that the
runtime resolves at call time: `store/bundle/key` reads the Dapr secret store, anything else reads
configuration. A plain-text header value is rejected at publish. Never put a token, key or password in
component JSON.

## Operations

| `operation` | Needs | Result |
|---|---|---|
| `callTool` (default) | `tool`, optional `arguments` | The tool's `structuredContent`, else `{ "text": … }` |
| `listTools` | — | `[{ name, description, inputSchema }]` |
| `readResource` | `uri` | The resource contents |
| `listResources` | — | The resources |
| `getPrompt` | `prompt` | The prompt |
| `listPrompts` | — | The prompts |

`timeoutSeconds` is 1..120 (default 20). Timeout answers 408, a transport failure 502, an unresolvable
secret 500, a host outside `Mcp:AllowedHosts` 403.

## As an AI agent tool

An agent (`Agents/*.json`, flow `sys-agents`) exposes the server's tools through a `kind: 3` tool bound
to the task. Only the names in `allow` are offered to the model, prefixed by `prefix`:

```json
{
  "kind": 3,
  "binding": {
    "task": { "key": "crm-mcp-task", "domain": "{domainName}", "flow": "sys-tasks", "version": "1.0.0" },
    "allow": ["search_customer"],
    "prefix": "crm_"
  }
}
```

Standalone, the task runs like any other task (`onEntries`, `onExits`, functions); an input mapping
may replace `config.arguments`.
