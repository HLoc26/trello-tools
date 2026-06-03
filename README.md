# trello-tools — Claude Code plugin marketplace

Team plugins for Trello-driven sprint work. Ships **trelol**, two commands:

| Command | Does |
|---|---|
| `/trelol:refine <board-id> [sprint-list]` | refines your "Doing" cards into structured specs + an "Implementation" checklist, with a human-in-the-loop ambiguity gate (answer now / defer to PO) |
| `/trelol:impl <card-id> [spec-file]` | implements a refined card in your codebase, working the checklist top-to-bottom and **ticking each item live** as it's verified — the Trello card becomes a real-time progress dashboard |

## Install (teammates) — two commands

The Trello server is a public npm package, so `npx` pulls it with no auth setup.

```bash
claude plugin marketplace add <your-org>/trello-tools     # the marketplace repo
claude plugin install trelol@trello-tools \
  --config trelloApiKey=YOUR_KEY --config trelloToken=YOUR_TOKEN
```

Restart Claude Code. Then in any session:

```
/trelol:refine <board-id> [sprint-list-name]   # spec + checklist
/trelol:impl <card-id>                          # do the work, tick as done
```

- **Auth:** none needed — runs on your existing `claude login` (Claude Team).
- **Secrets:** your Trello key/token are stored per-user by the install flow,
  never committed. Re-enter anytime with `/plugin configure trelol@trello-tools`.
- **Update:** `claude plugin marketplace update trello-tools && claude plugin update trelol`
  (restart to apply).

## Prerequisite: publish the Trello MCP server (one time, maintainer)

`trelol/.mcp.json` resolves the server via `npx -y @hloc/trello-mcp-server`,
published to **public npm**. Publish it once (maintainer):

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
└── trelol/
    ├── .claude-plugin/plugin.json    # manifest + userConfig (trello creds)
    ├── .mcp.json                     # declares the trello MCP server
    └── commands/
        ├── refine.md                 # /trelol:refine
        └── impl.md                   # /trelol:impl
```

## Validate after edits

```bash
claude plugin validate trello-tools/trelol --strict
claude plugin validate trello-tools
```
