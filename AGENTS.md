# AGENTS.md

## Cursor Cloud specific instructions

### Project overview

AccesibilityHub is an MCP (Model Context Protocol) Server for web accessibility testing. It communicates via **stdio transport** (not HTTP). It exposes tools (axe-core, Pa11y, Lighthouse, contrast checking) as MCP tools that AI assistants invoke.

### Runtime requirements

- **Node.js 20** (see `.nvmrc` for exact version). Use `nvm use` to activate.
- **pnpm 10.26.2** (declared in `package.json` `packageManager` field).
- **Puppeteer's Chrome** must be installed for Axe and Contrast adapter tests to pass. If tests fail with "browser not available", run `npx puppeteer browsers install chrome`.

### Available scripts

See `CONTRIBUTING.md` for the full list. Key commands:

- `pnpm test` — Vitest (219 tests, ~65s; Axe/Contrast adapter tests launch headless Chrome)
- `pnpm typecheck` — TypeScript strict checking
- `pnpm format:check` — Prettier (note: repo currently has pre-existing formatting warnings in 81 files)
- `pnpm build` — tsup build to `dist/`
- `pnpm dev` — tsup watch mode (rebuilds on changes)
- `pnpm start` — runs the built MCP server via `node dist/server.js`

### Running the MCP server

The server uses **stdio JSON-RPC 2.0**. To test interactively, pipe JSON-RPC messages:

```bash
echo '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}
{"jsonrpc":"2.0","method":"notifications/initialized"}
{"jsonrpc":"2.0","id":2,"method":"tools/list","params":{}}' | node dist/server.js 2>/dev/null
```

### Gotchas

- The `prebuild` script runs `scripts/check-node-version.js` which will fail if Node < 20.
- Puppeteer requires `--no-sandbox --disable-setuid-sandbox` flags in sandboxed/containerized environments. The adapters already set these flags.
- Some tests take ~2s each due to headless Chrome startup. The full suite is ~65 seconds.
