# Parta Instructional Design — MCP server

<img src="https://parta.io/wp-content/themes/parta/favicon.ico" alt="Parta" width="64" height="64" align="left" />

Design and build Parta courses directly from your AI coding assistant. This is Parta's hosted **Instructional Design** MCP server: create and edit course projects, sections, pages, and template-based content blocks, upload media, and assemble structured learning experiences — from any [MCP](https://modelcontextprotocol.io)-compatible client.

<br clear="left"/>

It's a single **remote** server — no local install, no dependencies. Point your client at the URL below and sign in when prompted.

| Field | Value |
| --- | --- |
| Name | `parta-inst-design` |
| Transport | Streamable HTTP (`http`) |
| URL | `https://mcp.parta.io/inst-design/v1beta` |
| Auth | OAuth (in-client sign-in on first use) |

## What it does

Once connected, your assistant can use the Parta Instructional Design tools to:

- Create and update course projects, sections, and pages
- Build content blocks from Parta templates
- Upload and manage media and files
- Assemble and reorganize structured courses

## Connect

Pick your client. Everything reduces to one URL: `https://mcp.parta.io/inst-design/v1beta`. On first use the client opens an OAuth sign-in — complete it to authorize access.

### Claude Code

Install as a plugin (bundles the server, no config file needed):

```text
/plugin marketplace add Paarta-io/parta-mcp-inst-design
/plugin install mcp-inst-design@parta-io
/reload-plugins
```

Or add the server directly from the CLI:

```bash
claude mcp add --transport http parta-inst-design https://mcp.parta.io/inst-design/v1beta
```

Then run `/mcp` and complete the OAuth flow when prompted.

### Cursor

Add to `~/.cursor/mcp.json` (global) or `.cursor/mcp.json` (per project):

```json
{
  "mcpServers": {
    "parta-inst-design": {
      "url": "https://mcp.parta.io/inst-design/v1beta"
    }
  }
}
```

### VS Code (GitHub Copilot)

Add to `.vscode/mcp.json` in your workspace:

```json
{
  "servers": {
    "parta-inst-design": {
      "type": "http",
      "url": "https://mcp.parta.io/inst-design/v1beta"
    }
  }
}
```

### Windsurf

Add to `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "parta-inst-design": {
      "serverUrl": "https://mcp.parta.io/inst-design/v1beta"
    }
  }
}
```

### Any other MCP client

Most clients accept a standard `mcpServers` block with an HTTP transport (Claude Desktop and others):

```json
{
  "mcpServers": {
    "parta-inst-design": {
      "type": "http",
      "url": "https://mcp.parta.io/inst-design/v1beta"
    }
  }
}
```

## MCP registry

This repository ships a [`server.json`](server.json) that follows the [official MCP registry](https://github.com/modelcontextprotocol/registry) schema, so the server can be published to the registry and discovered from any registry-aware client (including the [GitHub MCP Registry](https://github.com/mcp)).

Namespace: `io.parta/inst-design` (verified via the `parta.io` domain).

## Repository layout

```text
.
├── server.json            # MCP registry manifest (universal — any MCP client)
├── .claude-plugin/
│   ├── plugin.json        # Claude Code plugin manifest (declares the remote server)
│   └── marketplace.json   # Claude Code marketplace catalog (enables /plugin marketplace add)
└── README.md
```

## Links

- Website: https://parta.io
- Support: support@parta.io
