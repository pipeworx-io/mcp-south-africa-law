# @pipeworx/south-africa-law

South African Acts of Parliament from the South African Government's Acts
collection (Government Communication and Information System), where each Act
is published as gazetted with a summary page: search Acts by title or subject
words, then read an Act's record, the Government's summary, its commencement
statement and, where the gazetted PDF has a readable text layer, the text.
National Acts only; provincial legislation and regulations are not in this
collection.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 2020+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `za_law_search(query, page?)` — full-text search over the Acts pages, most
  relevant first. Returns id, title, Act number and year ("Act 13 of 2024"),
  Government Gazette number where shown and a link; 30 per page, `next_page`
  says whether to continue.
- `za_law_get(id, max_chars?, offset?)` — one Act: title, citation, summary,
  the commencement statement exactly as the page gives it, the gazetted PDF
  link(s) and the PDF text, capped at `max_chars` (use `offset` to read on).

## What this source is not

- Not a consolidation. Each Act is the text as published in the Government
  Gazette; later amendments are not applied.
- The page states no in-force flag, so `status` is always `null` with a `note`;
  nothing is derived. `commencement` carries what the page says, e.g.
  "To be proclaimed."
- Text comes from the gazette PDF. Older scanned Acts read through
  optical-recognition text with some recognition errors and flattened tables.
  Recent gazette PDFs often use font encodings the PDF reader drops letters
  from; the tool detects this and withholds the text with
  `empty_reason: text_unreadable` rather than return corrupted law. The PDF
  link is then the authoritative text.
- No section lookup: gazette PDF text does not keep section boundaries
  reliably.

## Auth

Keyless.

## Data sources

- South Africa, Government Communication and Information System — Acts of
  Parliament as published in the Government Gazette.

`data_as_of` is the date of the live read. Read live on each call; nothing is
copied. A search or lookup that finds nothing returns `empty_reason`, not an
empty success.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "south-africa-law": {
      "url": "https://gateway.pipeworx.io/south-africa-law/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/south-africa-law/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 2020+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/za_law_search \
  -H 'Content-Type: application/json' \
  -d '{"query":"expropriation"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/za_law_search`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-south-africa-law` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/south-africa-law/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "south-africa-law": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-south-africa-law"]
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
ask_pipeworx({ question: "your question about South Africa Law data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
