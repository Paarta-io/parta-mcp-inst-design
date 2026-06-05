# Parta Instructional Design — Claude Code plugin

<img src="https://parta.io/wp-content/themes/parta/favicon.ico" alt="Parta" width="64" height="64" align="left" />

Design and build Parta courses directly from Claude Code. This plugin connects Claude Code to Parta's hosted **Instructional Design** MCP server, so you can create and edit course projects, sections, pages, and template-based content blocks, upload media, and assemble structured learning experiences — all without leaving your terminal.

<br clear="left"/>

## Install

```text
/plugin marketplace add paarta-io/parta-mcp-inst-design
/plugin install parta-mcp-inst-design@parta
/reload-plugins
```

If the server requires sign-in, run `/mcp` and complete the OAuth flow when Claude Code prompts you. No local setup is needed — the plugin ships a single remote MCP server.

## What it does

Once installed, Claude Code can use the Parta Instructional Design tools to:

- Create and update course projects, sections, and pages
- Build content blocks from Parta templates
- Upload and manage media and files
- Assemble and reorganize structured courses

## Configuration

| Field | Value |
| --- | --- |
| Server | `parta-inst-design` |
| Transport | `http` (streamable HTTP) |
| URL | `https://mcp.parta.io/inst-design/v1beta` |

The server is declared inline in [`.claude-plugin/plugin.json`](.claude-plugin/plugin.json). The marketplace catalog that makes `/plugin marketplace add` work lives in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json).

## Repository layout

```text
.
├── .claude-plugin/
│   ├── plugin.json        # plugin manifest (declares the remote MCP server)
│   └── marketplace.json   # marketplace catalog (enables /plugin marketplace add)
└── README.md
```

## Links

- Website: https://parta.io
- Support: support@parta.io
