# SimpleVerifier MCP server

A remote [Model Context Protocol](https://modelcontextprotocol.io) server that gives ChatGPT, Claude, Cursor, VS Code and other MCP clients email and phone checks as tools.

- **Endpoint:** `https://simpleverifier.com/api/mcp` (Streamable HTTP)
- **Auth:** OAuth sign-in (ChatGPT, claude.ai, Claude Code) or `Authorization: Bearer <API key>`
- **Plan:** MCP and API access need a SimpleVerifier Pro, Business or Agency plan. Tool calls count against the plan's monthly API calls. [Pricing](https://simpleverifier.com/pricing)
- **Docs:** https://simpleverifier.com/docs/mcp

This repo holds the registry manifest (`server.json`) and ready-to-copy client configs. The server itself is hosted; there is nothing to install or run.

## Tools

| Tool | What it does |
| --- | --- |
| `verify_email` | One address: `valid`, `risky` or `invalid`, with a reason. Syntax, domain, disposable and live mailbox (SMTP) checks. |
| `verify_emails` | Up to 100 addresses in one call, deduplicated. Returns every result, or a `job_id` for `get_job_results`. |
| `get_job_results` | Progress and per-address results of a `verify_emails` job, paged. |
| `check_phone_format` | Format, E.164 form, country and numbering-plan line type. A format check only: it never says whether a number is in service. |
| `check_domain` | A domain's MX, SPF and DMARC records, disposable status, and an optional catch-all probe. |
| `get_usage` | Your plan, what it includes, this month's usage and the rate limit. |

Only `valid` means the mail server confirmed the mailbox. Anything unconfirmed (catch-all domains, providers that hide mailbox status, servers that don't answer) comes back `risky`.

## Connect

**Claude Code**

```bash
claude mcp add --transport http simpleverifier https://simpleverifier.com/api/mcp
# then run /mcp inside a session to sign in, or pass a key:
claude mcp add --transport http simpleverifier https://simpleverifier.com/api/mcp --header "Authorization: Bearer YOUR_API_KEY"
```

**ChatGPT and claude.ai:** add a custom connector with the URL `https://simpleverifier.com/api/mcp`, choose OAuth, then sign in on the SimpleVerifier screen. Step-by-step: https://simpleverifier.com/docs/mcp

**Cursor, VS Code, Claude Desktop, Windsurf:** copy the matching file from [`configs/`](configs):

| Client | File | Goes in |
| --- | --- | --- |
| Cursor | `cursor.mcp.json` | `~/.cursor/mcp.json` or `.cursor/mcp.json` |
| VS Code | `vscode.mcp.json` | `.vscode/mcp.json` (asks for the key once, stores it securely) |
| Claude Desktop | `claude_desktop_config.json` | Settings > Developer > Edit config (uses `mcp-remote`, needs Node.js) |
| Windsurf / Devin Desktop | `windsurf.mcp_config.json` | `~/.config/devin/mcp_config.json` (older builds: `~/.codeium/windsurf/mcp_config.json`) |

Create an API key at https://simpleverifier.com/api-keys. Keys start with `sv_live_`. Keep them out of shared config files and repositories.

**Try it with curl**

```bash
curl https://simpleverifier.com/api/mcp \
  -H "Authorization: Bearer $SIMPLEVERIFIER_KEY" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"verify_email","arguments":{"email":"jane@company.com"}}}'
```

## Related

- REST API: https://simpleverifier.com/docs/api
- SDKs: [Node.js](https://github.com/Ahmedmkarrar/simpleverifier-node), [Python](https://github.com/Ahmedmkarrar/simpleverifier-python), [PHP](https://github.com/Ahmedmkarrar/simpleverifier-php), [Go](https://github.com/Ahmedmkarrar/simpleverifier-go), [Ruby](https://github.com/Ahmedmkarrar/simpleverifier-ruby)
- Support: support@simpleverifier.com

## License

MIT for the files in this repo. The hosted service is covered by the [SimpleVerifier terms](https://simpleverifier.com/terms).
