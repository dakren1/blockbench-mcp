# Blockbench MCP

MCP server + Blockbench plugin that lets an AI client (Claude Code, Claude Desktop, Cursor) model, texture, rig and animate Minecraft models in the Blockbench desktop app. Fork of [sosadly/blockbench-mcp](https://github.com/sosadly/blockbench-mcp), migrated to pnpm.

## How it works

```
AI client --stdio (MCP)--> blockbench-mcp (Node, src/ -> dist/) --HTTP 127.0.0.1:8787--> Blockbench + plugin/blockbench_mcp.js
```

- **Bridge plugin** (`plugin/blockbench_mcp.js`): runs inside Blockbench desktop and executes each command against the Blockbench API.
- **MCP server** (`src/`): exposes the tools over stdio and forwards calls to the bridge.

## Setup

Requires Blockbench desktop 4.8+ and Node 18+.

```bash
pnpm install
pnpm build
```

1. In Blockbench: **File > Plugins > Load Plugin from File**, pick `plugin/blockbench_mcp.js`, and allow network access ("Always allow for this plugin").
2. Register the server with your client (absolute path):

   ```bash
   claude mcp add blockbench -- node /absolute/path/to/blockbench-mcp/dist/index.js
   ```

3. Check the bridge: `curl http://127.0.0.1:8787/ping`

| Env var | Default | Purpose |
|---------|---------|---------|
| `BLOCKBENCH_MCP_PORT` | `8787` | Must match the plugin's port setting |
| `BLOCKBENCH_MCP_HOST` | `127.0.0.1` | Bridge host |

## Security

- The bridge binds to `127.0.0.1` only and refuses requests with an `Origin` header, a non-loopback `Host`, or a non-JSON `Content-Type`.
- **`execute_script` is disabled by default.** It runs unsandboxed JavaScript with Blockbench's full privileges. To enable it, turn on **Settings > General > Allow execute_script** (only with a client and content you trust: prompt injection).
- File tools (`load_project`, `load_reference`, `export_*`) and `install_plugin` accept arbitrary paths/URLs. Review those calls before approving them.

## Development

```bash
pnpm dev     # tsc --watch
pnpm build   # compile to dist/
pnpm test    # build + run tests
```

Tool definitions are in `src/tools.ts`; bridge handlers are in the `commands` object of `plugin/blockbench_mcp.js`.

## Credits

MIT. Original project by [sosadly](https://github.com/sosadly/blockbench-mcp).
