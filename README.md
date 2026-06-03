# trello-tools — Claude Code plugin marketplace

Team plugins for Trello-driven sprint work. Ships the **refiner**: turns your
"Doing" cards into structured specs + implementation checklists, with a
human-in-the-loop ambiguity gate (answer now / defer to PO).

## Install (teammates) — two commands

The Trello server is a public npm package, so `npx` pulls it with no auth setup.

```bash
claude plugin marketplace add <your-org>/trello-tools     # the marketplace repo
claude plugin install refiner@trello-tools \
  --config trelloApiKey=YOUR_KEY --config trelloToken=YOUR_TOKEN
```

Restart Claude Code. Then in any session:

```
/refine <board-id> [sprint-list-name]
```

- **Auth:** none needed — runs on your existing `claude login` (Claude Team).
- **Secrets:** your Trello key/token are stored per-user by the install flow,
  never committed. Re-run interactively any time with
  `/plugin configure refiner@trello-tools`.
- **Update:** `claude plugin update refiner` (restart to apply).

## Prerequisite: publish the Trello MCP server (one time, maintainer)

`refiner/.mcp.json` resolves the server via
`npx -y @hloc/trello-mcp-server`, published to **public npm**. Publish
it once (maintainer):

```bash
cd ../trello-mcp-server
npm login                      # npmjs account (@hloc = your personal scope)
npm publish --access public --otp=<code>   # OTP if 2FA is on; prepublishOnly builds
```
Verify at: https://www.npmjs.com/package/@hloc/trello-mcp-server

## Layout

```
trello-tools/
├── .claude-plugin/marketplace.json   # makes the repo installable
└── refiner/
    ├── .claude-plugin/plugin.json    # manifest + userConfig (trello creds)
    ├── .mcp.json                     # declares the trello MCP server
    └── commands/refine.md            # the /refine command
```

## Validate after edits

```bash
claude plugin validate trello-tools/refiner --strict
claude plugin validate trello-tools
```
