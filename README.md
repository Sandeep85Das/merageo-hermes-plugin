# meraGEO AI Visibility for Hermes Agent

Check how often ChatGPT, Claude and Gemini mention a website when people ask buying questions in its category, and request a new scan, from Hermes Agent.

This is a configuration-only [Agent Plugins v1](https://agent-plugins.org) package: `plugin.json`, `mcp.json` and one workflow skill. It contains no code. It connects Hermes to meraGEO's hosted MCP server at `https://merageo.com/api/mcp/agent`.

meraGEO is a product of Okyasoft Pte Ltd, Singapore.

## Tools

| Tool | What it does |
|---|---|
| `get_ai_visibility_report` | Reads the latest stored report for a domain: a 0–100 visibility score, how many of the sampled answers from each model mention the site, and the top issues found (for example a missing `llms.txt`, schema markup, meta description or sitemap). Read-only. |
| `start_ai_visibility_scan` | Requests a new scan of a public domain. meraGEO reads the site's public pages and asks several AI models category questions; it takes a few minutes. Reuses a recent report if one exists. |
| `get_scan_status` | Whether a scan is still running, complete (with its score) or failed. Read-only. |
| `get_profile` | The connected meraGEO account's id, display name and a masked email. Read-only. |

The tool list is served by the remote server at runtime.

## Setup

meraGEO requires a sign-in. Every tool needs a meraGEO account (free to create, Google sign-in) and the `geo:scan` OAuth scope. There are no anonymous tools and no API keys.

1. Install and enable the plugin:

   ```bash
   hermes plugins install merageo
   ```

2. Add the server to `~/.hermes/config.yaml` with OAuth. Portable packages cannot declare OAuth themselves, so this entry is required; it replaces the package's entry, so keep `trust: untrusted`:

   ```yaml
   mcp_servers:
     merageo:
       url: "https://merageo.com/api/mcp/agent"
       auth: oauth
       trust: untrusted
   ```

3. Sign in:

   ```bash
   hermes mcp login merageo
   ```

   Hermes opens meraGEO's consent page in your browser. Sign in with Google and approve the `geo:scan` scope. Hermes stores and refreshes the token; this plugin never sees it.

Then ask, for example: "How visible is example.com in AI assistant answers?"

## Disclosure

- **Network:** hosted-only connector with no local code. Every tool call goes to `merageo.com` and acts as the signed-in meraGEO user.
- **Writes:** `start_ai_visibility_scan` starts a real scan: meraGEO fetches the domain's public pages and queries ChatGPT, Claude and Gemini through its own accounts. Scans are rate-limited per account. The package marks the server `untrusted`, so Hermes asks before write-capable calls and refuses them in unattended runs.
- **Credentials:** OAuth tokens are stored and refreshed by Hermes (`~/.hermes/mcp-tokens/`), not by this plugin.
- **Data:** meraGEO stores scan results for the domain, and the domain's scan page on merageo.com is public. meraGEO logs requests for operations and abuse prevention; there is no telemetry from this package. See [merageo.com/privacy](https://merageo.com/privacy) and [merageo.com/terms](https://merageo.com/terms).
- **No self-updating:** updates arrive only through a reviewed catalog SHA bump.

## Support

[merageo.com/support](https://merageo.com/support) · hello@merageo.com

## License

MIT. Copyright (c) 2026 Okyasoft Pte Ltd.
