# @pipeworx/denmark-law

Danish national legislation from Retsinformation, the official legal
information system of the Ministry of Justice: search acts, consolidated acts
(lovbekendtgoerelser) and executive orders by title or popular name, then read
one document's record and its text, whole or one section (§) at a time.
Original Danish text, with citation, publication, status and validity date.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1905+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `dk_law_search(query, document_type?, in_force_only?, page?)` — find
  documents by Danish title words or popular name (for example
  `databeskyttelsesloven`, `straffeloven`). By default searches acts and
  consolidated acts and returns only those in force today; `document_type`
  can be `act`, `consolidated_act` or `executive_order`. Returns id (an ELI
  path such as `lta/2024/289`), citation, title, popular name, type,
  ministry, status, `title_match` and a link. 10 per page; `next_page` says
  whether to continue. The search also matches body text, so later hits may
  not carry the words in their title; `title_match` says which do.
- `dk_law_get(id, section?, max_chars?)` — one document by id: title,
  popular name, type, citation, ministry, publication, status (valid or
  historic), validity date, EU references and the text. `section` returns one
  § (`"6"`, `"10 a"`); otherwise the text is capped at `max_chars` with a
  contents list. Annexes (such as an EU regulation printed with the act) are
  not part of the returned text.

## Auth

Keyless.

## Data sources

- Denmark, Ministry of Justice, Retsinformation — acts, consolidated acts and
  executive orders.

A Danish law is read through its latest consolidated act. A document that has
been consolidated or replaced is marked historic, and the search leaves it out
unless `in_force_only=false`. The consolidated text does not include acts
passed after the consolidation, and the response says so. Read live on each
call; nothing is copied. `data_as_of` is the date of the live read. A search
or lookup that finds nothing returns `empty_reason`, not an empty success. EU
law is not in this pack (see the EUR-Lex pack), and court decisions are out of
scope.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "denmark-law": {
      "url": "https://gateway.pipeworx.io/denmark-law/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/denmark-law/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1905+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/dk_law_search \
  -H 'Content-Type: application/json' \
  -d '{"query":"databeskyttelsesloven"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/dk_law_search`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-denmark-law` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/denmark-law/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "denmark-law": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-denmark-law"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Denmark Law data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
