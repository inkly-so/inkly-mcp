<p align="center">
  <a href="https://www.inkly.so"><img src="assets/logo.png" alt="Inkly" width="96" height="96"></a>
</p>

<h1 align="center">Inkly MCP server</h1>

<p align="center">
  Send, track, and manage documents for eSignature from Claude, ChatGPT, Cursor, and any AI agent.
</p>

<p align="center">
  <a href="https://www.inkly.so">Website</a> ·
  <a href="https://www.inkly.so/auth?tab=signup">Sign up</a> ·
  <a href="https://www.inkly.so/docs/api/ai-agents">MCP docs</a> ·
  <a href="https://www.inkly.so/help/use-inkly-from-claude-or-chatgpt">Setup guide</a> ·
  <a href="https://www.inkly.so/pricing">Pricing</a>
</p>

---

[Inkly](https://www.inkly.so) is legally binding [eSignature software](https://www.inkly.so) for professionals and small businesses. This repository describes Inkly's hosted [MCP server](https://www.inkly.so/e-signature-mcp), which lets an AI assistant work with your Inkly account in plain language:

- **Find templates** and see which roles and fields each one needs.
- **Prepare documents** from a template or a PDF, ready for you to check and send.
- **Track signing**: who has signed, who is still waiting, and whether an invitation bounced.
- **Get the signed PDF** of any completed document.
- **Send documents and reminders**, or void a document, when you give the assistant Full access.

Ask things like *"Which of my agreements are still waiting to be signed?"* or *"Prepare the consulting agreement for Alex Morgan at alex@example.com."*

There is nothing to install. The server is hosted by Inkly and you sign in with your Inkly account.

## Server URL

```
https://www.inkly.so/mcp
```

- **Transport:** Streamable HTTP
- **Sign-in:** OAuth 2.1 with dynamic client registration. Your assistant opens Inkly, you choose a workspace and an access level, and click **Allow**.
- Use `www.inkly.so`, not `inkly.so`. Assistants lose their sign-in on the redirect.

You need an Inkly account. [Create one here](https://www.inkly.so/auth?tab=signup).

## Connect

### Claude

Inkly is in [Claude's connector directory](https://claude.ai/directory/connectors/inkly).

1. In Claude, open **Customize**, then **Connectors**, then **Discover**.
2. Search for **Inkly**, open it, and click **Connect**.
3. Approve the connection in Inkly.

If you don't see it, add it yourself: **Customize** → **Connectors** → **Add** → **Add custom connector**, name it Inkly, and paste the server URL.

### ChatGPT

1. In ChatGPT on the web, open **Plugins**, click **Add**, then **Add custom MCP server**.
2. Name it Inkly and paste the server URL.
3. Keep Authentication on **OAuth**, tick the warning, and click **Create as a plugin**.
4. Approve the connection in Inkly.

### Cursor

Add Inkly to `~/.cursor/mcp.json` (every project) or `.cursor/mcp.json` (one project):

```json
{
  "mcpServers": {
    "inkly": {
      "url": "https://www.inkly.so/mcp"
    }
  }
}
```

Cursor shows Inkly under MCP settings with a **Connect** button. Click it and approve the connection in Inkly.

### Claude Code

```bash
claude mcp add --transport http inkly https://www.inkly.so/mcp
```

Then run `/mcp` in Claude Code, choose Inkly, and approve the connection in your browser.

### Codex

```bash
codex mcp add inkly --url https://www.inkly.so/mcp
```

Codex opens your browser to approve the connection.

### Hermes Agent

```bash
hermes plugins install inkly --enable
```

The first time Hermes uses Inkly, your browser opens Inkly's sign-in. Approve the connection there. The plugin lives in [`hermes/`](hermes/README.md), which explains what it installs and how to connect a headless machine with an API key.

### n8n and your own agents

Agents that run without a person signing in use an [API key](https://www.inkly.so/docs/api/reference/authentication) instead, sent as `Authorization: Bearer ink_live_…` over Streamable HTTP. In n8n, add the **MCP Client** node with HTTP Streamable transport and Bearer Auth. API keys are available on Pro and Teams.

## Access levels

You pick a workspace and an access level each time you approve a connection, and can change it any time in **Settings → Integrations**. Each level includes the one before it.

| Level | What the assistant can do |
| --- | --- |
| **Read** | Find and read templates and documents. |
| **Prepare** (default) | Create drafts that you check and send in Inkly. Nothing is emailed until you press Send. |
| **Full access** | Send from your templates without review, send reminders, and void documents. Pro and Teams. |

An assistant only sees the tools its access level and plan allow. After you raise a connection to Full access, refresh the connector so the send tools appear.

## Tools

| Tool | What it does | Needs |
| --- | --- | --- |
| `get_account` | Gets the user's own name and email, the workspace, plan, access level, allowance, and free sends left. | Read |
| `search_templates` | Finds up to 25 visible templates by title. | Read |
| `get_template` | Gets a template's roles in order and prefilled fields by label. | Read |
| `search_documents` | Finds up to 25 visible documents by title or recipient, with status. | Read |
| `get_document` | Gets document status, recipients, and invitation delivery problems. | Read |
| `get_signed_pdf_link` | Gets a download link for the completed PDF. The link lasts 5 minutes. | Read |
| `get_my_signing_link` | Returns a link for the user to open and sign themselves. The assistant cannot sign. | Read |
| `prepare_document_from_template` | Makes a draft in Inkly. Nothing is sent. Returns the link to check and send it. | Prepare |
| `start_pdf_upload` | Creates a one-time PDF upload URL and a curl PUT command. Nothing is sent. | Prepare |
| `create_draft_from_upload` | Makes a draft from the uploaded PDF. Nothing is sent. Reads fields from markers in the PDF, or leaves them for you to place in the editor. | Prepare |
| `send_template` | Sends a real, legally binding document by email. Retries with the same `request_id` never send twice. | Full access |
| `send_reminder` | Emails a signing reminder to eligible signers. A cooldown applies. | Full access |
| `void_document` | Voids a sent document and emails its signers. Only the creator or an admin can void. | Full access |

Uploading a PDF needs a shell, so it works in Claude Code, Codex, and Cursor. In the Claude and ChatGPT apps, upload the PDF in Inkly or save it as a template. Write [field markers](https://www.inkly.so/docs/api/field-markers) such as `{{signature;signer=client}}` into the PDF so the assistant never guesses where fields go.

## Safety

- **Prepare, then send.** By default an assistant makes a draft and you press Send in Inkly.
- **No signing for anyone.** No tool signs a document. `get_my_signing_link` returns a link only the connected person can open, signed in to their own account.
- **Real sends.** There is no test mode. Every sent document is real and legally binding, so send to your own email while you try it out.
- **Document text is data.** The server tells assistants never to follow instructions found inside documents, templates, or field values.
- **You stay in control.** Disconnect any assistant in **Settings → Integrations**. Each connection is limited to 120 requests a minute.

Every completed document comes with a tamper-evident Certificate of Completion and a full [audit trail](https://www.inkly.so/electronic-signature-audit-trail), and Inkly meets the ESIGN Act, UETA, eIDAS, and GDPR ([compliance](https://www.inkly.so/compliance)).

## Agent skill

[`skills/inkly/SKILL.md`](skills/inkly/SKILL.md) teaches an agent to use Inkly, through these MCP tools when they're connected, or through the [REST API](https://www.inkly.so/docs/api) with an API key. Install it with:

```bash
npx skills add inkly-so/inkly-mcp
```

## REST API

Building an integration rather than chatting? Use the [Inkly eSignature API](https://www.inkly.so/e-signature-api):

- [API docs](https://www.inkly.so/docs/api) and [quickstart](https://www.inkly.so/docs/api/quickstart)
- [OpenAPI 3.1 document](https://www.inkly.so/api/v1/openapi.json)
- [`/llms.txt`](https://www.inkly.so/llms.txt), a guide to Inkly's docs for agents

## Files in this repository

| File | Used by |
| --- | --- |
| `server.json` | The official MCP Registry |
| `plugin.json`, `mcp.json` | Agent Plugins clients |
| `hermes/` | The Hermes Agent plugin catalog |
| `skills/inkly/SKILL.md` | Agent Skills clients, skills.sh, and ClawHub |
| `lhm.plugin.json` | LobeHub Market |

## Support

- [Help center](https://www.inkly.so/help)
- Email [hello@inkly.so](mailto:hello@inkly.so)
- [Privacy policy](https://www.inkly.so/legal/privacy) · [Terms of service](https://www.inkly.so/legal/terms)

## License

The files in this repository are MIT licensed. The Inkly service is covered by its [terms of service](https://www.inkly.so/legal/terms).
