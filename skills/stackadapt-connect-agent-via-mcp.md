---
name: Connect an agent to StackAdapt over MCP
description: >-
  Authorize an MCP client against StackAdapt's hosted Model Context Protocol server using the
  OAuth 2.1 discovery chain the provider publishes, so an agent can read campaign intelligence
  without a hand-issued API key.
api: mcp/stackadapt-mcp.yml
endpoint: https://mcp.stackadapt.com/
operations: []
generated: '2026-08-13'
method: generated
source: >-
  Probed from https://mcp.stackadapt.com/.well-known/oauth-protected-resource and
  https://www.stackadapt.com/.well-known/oauth-authorization-server on 2026-08-13.
---

# Connect an agent to StackAdapt over MCP

StackAdapt runs a **hosted, remote** MCP server. There is no package to install and nothing to
run locally — an agent POSTs JSON-RPC to a URL. The whole job is getting a token.

## The endpoint

```
https://mcp.stackadapt.com/
```

The MCP endpoint is the **host root**. `https://mcp.stackadapt.com/mcp` returns 404 — do not
append a path.

## Discovery chain

1. Call the endpoint with no credential. You get `401` and a conformant challenge:

   ```
   WWW-Authenticate: Bearer error="invalid_token",
     error_description="Missing Authorization header",
     resource_metadata="https://mcp.stackadapt.com/.well-known/oauth-protected-resource/"
   ```

2. Fetch the resource metadata (RFC 9728). It names the authorization server and the scopes:

   ```json
   {
     "resource": "https://mcp.stackadapt.com/",
     "authorization_servers": ["https://www.stackadapt.com/"],
     "scopes_supported": ["graphql-public:read", "graphql-public:write"]
   }
   ```

3. Fetch `https://www.stackadapt.com/.well-known/oauth-authorization-server` (RFC 8414) for the
   endpoints: `/oauth/authorize`, `/oauth/token`, `/oauth/register`, `/oauth/revoke`,
   `/oauth/introspect`.

4. Register dynamically at `https://www.stackadapt.com/oauth/register` (RFC 7591) — no
   pre-registration required.

5. Run `authorization_code` with **PKCE `S256`**, or `client_credentials` for a headless agent.

## Scope guidance

Request **`graphql-public:read` only** unless the agent genuinely needs to mutate. Two facts
make write scope expensive here:

- The scopes are **coarse**. Two scopes cover all 55 root queries and 97 root mutations of the
  GraphQL API — a write token can create, archive, pause and delete across every advertiser the
  account can reach.
- The authorization server metadata advertises **only `graphql-public:read`** in
  `scopes_supported`, while the resource metadata advertises both. Expect the authorization
  server to be the binding constraint and handle a narrowed grant.

Also note `code_challenge_methods_supported` includes `plain`. **Always send `S256`.**

## After authorization

Call `tools/list` to discover the tool set. It is not published anywhere public — StackAdapt's
`llms.txt` describes the server's capabilities in prose (campaign configuration, performance
metrics, creative assets across CTV, display, native, audio, DOOH and programmatic linear TV)
but names no tools, and `docs.stackadapt.com` serves `User-agent: * / Disallow: /`. Introspect;
do not assume.

## Failure modes

| Symptom | Meaning |
|---|---|
| `401 {"error":"invalid_token"}` | No or expired bearer token. Re-run the chain. |
| `404 Cannot POST /mcp` | You appended a path. Use the host root. |
| Token works for MCP but not GraphQL | Different credential systems — see the note below. |

## Do not confuse the three credentials

StackAdapt has **three** unrelated auth schemes. An OAuth token minted for the MCP resource is
not a GraphQL API key, and neither is the REST key:

- REST v2 (`https://api.stackadapt.com/service/v2`) — `X-Authorization` header, static key.
- GraphQL (`https://api.stackadapt.com/graphql`) — `Authorization: Bearer`, a **different**
  static key.
- MCP (`https://mcp.stackadapt.com/`) — OAuth 2.1 access token.

See `authentication/stackadapt-authentication.yml`.
