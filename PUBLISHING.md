# Publishing to the MCP Registry

This repo publishes [`server.json`](server.json) to the official **MCP Registry**
(`registry.modelcontextprotocol.io`) via the manual GitHub Actions workflow
[`.github/workflows/publish-mcp.yml`](.github/workflows/publish-mcp.yml). Once
accepted, the server syncs automatically into the [GitHub MCP Registry](https://github.com/mcp).

Namespace: **`io.parta/inst-design`**, authorized by proving control of the
**`parta.io`** domain (DNS). Verifying the apex also covers `io.parta.*` subnamespaces.

> **This repository is public.** The signing key is never committed — it lives only
> in `key.pem` on the maintainer's machine (gitignored) and as a GitHub **organization
> secret**. `workflow_dispatch` can be triggered only by users with write access.

---

## One-time setup

> The key, the DNS record and the org secret are **shared by every `io.parta/*` server**,
> including [`parta-mcp-block-builder`](https://github.com/Paarta-io/parta-mcp-block-builder). If they already
> exist, skip steps 1–2 and in step 3 only grant this repo access and create its environment.

### 1. Generate the Ed25519 signing key

Requires **OpenSSL 3.0+**. macOS ships LibreSSL (no Ed25519) — install `brew install openssl@3`
and call it explicitly, e.g. `/opt/homebrew/opt/openssl@3/bin/openssl` (Apple Silicon)
or `/usr/local/opt/openssl@3/bin/openssl` (Intel).

```bash
openssl genpkey -algorithm Ed25519 -out key.pem   # keep this file safe & private
```

### 2. Add the DNS TXT record (apex of parta.io)

```bash
PUBLIC_KEY="$(openssl pkey -in key.pem -pubout -outform DER | tail -c 32 | base64)"
echo "parta.io.  IN TXT  \"v=MCPv1; k=ed25519; p=${PUBLIC_KEY}\""
```

Add the printed value as a **TXT record on the apex** `parta.io` (not under a
`_mcp-auth` / `_mcp-registry` selector — MCP uses SPF-style apex placement).
Wait for DNS to propagate (`dig +short TXT parta.io` should show it).

**Current record** (public key of the key in use — safe to share):

| Field | Value |
| --- | --- |
| Type | `TXT` |
| Host / Name | `@` (the apex `parta.io`, no subdomain) |
| Value | `v=MCPv1; k=ed25519; p=5CnXvuT4Z1kU59YFhXYt7kRPTANgXQcsDove49LjyuU=` |
| TTL | default (e.g. 3600) |

```text
parta.io.  IN TXT  "v=MCPv1; k=ed25519; p=5CnXvuT4Z1kU59YFhXYt7kRPTANgXQcsDove49LjyuU="
```

Add it **next to** the existing TXT records on `parta.io` (SPF, site verifications) —
do not edit or replace them. After a key rotation, update this table too.

Check it is live:

```bash
dig +short TXT parta.io | grep MCPv1
```

### 3. Add the private key as a GitHub organization secret

Extract the private key as hex (64 chars):

```bash
openssl pkey -in key.pem -noout -text | grep -A3 "priv:" | tail -n +2 | tr -d ' :\n'; echo
```

Then in GitHub:

1. **Organization settings → Secrets and variables → Actions → New organization secret**:
   **`MCP_PRIVATE_KEY`** = the hex string above.
2. **Repository access** → **Selected repositories** → only the `parta-mcp-*` repos that
   publish to the registry (this one included). Never "All repositories".
3. In this repo: **Settings → Environments → New environment** → name it exactly
   `mcp-registry-publish` (no secret needed there — it is the gate the workflow runs behind).
4. **Deployment branches and tags** → restrict to `main` only.
5. Recommended: add yourself as a **Required reviewer** so each publish pauses for approval.

The key can publish/overwrite *any* `io.parta/*` server, so these protections matter
on a public repo. Rotate by generating a new key, updating the TXT record (remove the
old one) and the secret.

---

## Publishing

1. GitHub → **Actions → Publish to MCP Registry → Run workflow**.
2. Optionally set **version** (e.g. `1.0.1`). The registry rejects re-publishing an
   existing version, so bump it whenever `server.json` hasn't already been bumped.
3. The run validates `server.json`, authenticates via DNS, publishes, and prints the
   live listing.

Verify manually anytime:

```bash
curl -s "https://registry.modelcontextprotocol.io/v0/servers?search=io.parta/inst-design" \
  | jq '.servers[].server | {name, version}'
```

## Local publish (alternative)

```bash
brew install mcp-publisher   # or download from the registry releases
mcp-publisher validate server.json
PRIVATE_KEY="$(openssl pkey -in key.pem -noout -text | grep -A3 'priv:' | tail -n +2 | tr -d ' :\n')"
mcp-publisher login dns --algorithm ed25519 --domain parta.io --private-key "$PRIVATE_KEY"
mcp-publisher publish
```
