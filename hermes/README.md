# Inkly for Hermes Agent

Send, track, and manage documents for eSignature from [Hermes Agent](https://hermes-agent.nousresearch.com) with [Inkly](https://www.inkly.so).

[Website](https://www.inkly.so) · [Sign up](https://www.inkly.so/auth?tab=signup) · [Inkly MCP server](https://www.inkly.so/e-signature-mcp) · [MCP docs](https://www.inkly.so/docs/api/ai-agents) · [Pricing](https://www.inkly.so/pricing)

[Inkly](https://www.inkly.so) is legally binding [eSignature software](https://www.inkly.so) for professionals and small businesses. This plugin connects Hermes to Inkly's hosted MCP server and bundles a skill that teaches the agent to use it safely:

- **Find templates** and see which roles and fields each one needs.
- **Prepare documents** from a template or a PDF, ready for you to check and send.
- **Track signing**: who has signed, who is still waiting, and whether an invitation bounced.
- **Get the signed PDF** of any completed document.
- **Send documents and reminders**, or void a document, when you give Hermes Full access.

Ask things like *"Which of my agreements are still waiting to be signed?"* or *"Prepare the consulting agreement for Alex Morgan at alex@example.com."*

## Install

```bash
hermes plugins install inkly --enable
```

You need an Inkly account. [Create one here](https://www.inkly.so/auth?tab=signup).

The first time Hermes uses Inkly, your browser opens Inkly's sign-in. Choose a workspace and an access level, and click **Allow**:

| Level | What Hermes can do |
| --- | --- |
| **Read** | Find and read templates and documents. |
| **Prepare** (default) | Create drafts that you check and send in Inkly. Nothing is emailed until you press Send. |
| **Full access** | Send from your templates, send reminders, and void documents. Pro and Teams. |

Change the level or disconnect Hermes any time in Inkly under **Settings → Integrations**.

## What to know before you install

- **Network.** Every tool call goes to `www.inkly.so` and acts on your Inkly account, at the access level you approved. The tools come from Inkly's server, so they can change without a new plugin version.
- **Sign-in.** Hermes connects through [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) `0.1.49`, run with `npx` (needs Node.js 20.18.1 or later and npm). On first use it opens your browser and a local callback port, and stores its OAuth tokens in `~/.mcp-auth`. No keys go in your Hermes config.
- **Approvals.** The plugin marks the server untrusted, so Hermes asks before every tool that isn't read-only, such as sending a document, and refuses those tools in unattended runs.
- **Real sends.** There is no test mode. A sent document emails real people and is legally binding, so send to your own email while you try it out.
- **No code.** The plugin is configuration only: no scripts, binaries, telemetry, or self-updater.

## Headless machines

Where no browser can open, skip the plugin and add the server to `~/.hermes/config.yaml` with a Pro or Teams [API key](https://www.inkly.so/docs/api/reference/authentication):

```yaml
mcp_servers:
  inkly:
    url: https://www.inkly.so/mcp
    headers:
      Authorization: Bearer ink_live_...
```

## Support

- [Help center](https://www.inkly.so/help)
- Email [hello@inkly.so](mailto:hello@inkly.so)
- [Privacy policy](https://www.inkly.so/legal/privacy) · [Terms of service](https://www.inkly.so/legal/terms)

The files in this folder are MIT licensed. The Inkly service is covered by its [terms of service](https://www.inkly.so/legal/terms).
