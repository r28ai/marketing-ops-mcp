# Marketing Ops

Shipped work becomes blog drafts; competitor pricing, mentions and webinars run on schedule.

An MCP server with **13 workflows** across Linear, GitHub, Google Docs, Slack, Firecrawl, Google Sheets, Tavily, Google Forms, Google Calendar, Gmail, Stripe, Granola and Notion. Each workflow is a prompt your agent runs as a slash command, over the 27 tools it needs and no others.

```bash
uv tool install https://github.com/r28ai/marketing-ops-mcp/releases/download/v0.1.0/marketing_ops_mcp-0.1.0-py3-none-any.whl
claude mcp add marketing -- marketing-ops-mcp
```

It installs with [uv](https://docs.astral.sh/uv/) from this repository's release, with no git and nothing to build; nothing but Charter and the libraries it uses comes from PyPI. To update, run the install line from the [latest release](https://github.com/r28ai/marketing-ops-mcp/releases/latest). If a desktop app cannot find `marketing-ops-mcp`, give it the full path from `which marketing-ops-mcp` (`where marketing-ops-mcp` on Windows).

Then ask your agent to **connect your apps**, or run `/mcp__marketing__setup`.

## Connect your apps

Ask the agent to connect one ("connect Linear"). It tells you where to get that app's key and the command that stores it, and the next call works, with no restart. The agent never asks for a key in the chat.

Or connect everything this server uses from a terminal:

```bash
marketing-ops-mcp login            # each app in turn
marketing-ops-mcp login linear     # just one
marketing-ops-mcp status           # what is connected
```

Tokens and keys go to your operating system's keychain (macOS Keychain, Windows Credential Manager, the Secret Service on Linux), and are checked with one read-only call to the app's own API before they are kept. Every key, token and OAuth client is yours: we register no app with any of these services, and nothing passes through a server of ours, because there isn't one.

| App | How it connects | Or set |
|---|---|---|
| Linear | Your own key ([get one](https://linear.app/settings/account/security)), entered once. | `LINEAR_API_KEY` |
| GitHub | Your own key ([get one](https://github.com/settings/tokens/new?description=Charter&scopes=repo,read:user)), entered once. | `GITHUB_TOKEN` |
| Google | Browser sign-in, over your own OAuth client ([make one](https://docs.r28.ai/charter/auth/setup/google)). | `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET` |
| Slack | Your own key ([get one](https://docs.r28.ai/charter/auth/setup/slack)), entered once. A bot token from your own Slack app, which the guide sets up in about three minutes. | `SLACK_BOT_TOKEN` |
| Firecrawl | Your own key ([get one](https://www.firecrawl.dev/app/api-keys)), entered once. | `FIRECRAWL_API_KEY` |
| Tavily | Your own key ([get one](https://app.tavily.com/home)), entered once. | `TAVILY_API_KEY` |
| Stripe | Your own key ([get one](https://dashboard.stripe.com/apikeys)), entered once. | `STRIPE_API_KEY` |
| Granola | Your own key ([get one](https://docs.granola.ai/help-center/sharing/integrations/granola-api)), entered once. In the Granola app: Settings → Connectors → API keys. Business plan or above. | `GRANOLA_API_KEY` |
| Notion | Your own key ([get one](https://www.notion.so/profile/integrations)), entered once. Then share the pages it should see with the integration. | `NOTION_API_KEY` |

A variable set in your client's config always wins over the keychain.

## Workflows

| Workflow | What you get | Apps |
|---|---|---|
| **What shipped → blog and social drafts** <br>`what_shipped_to_blog_and_social_drafts` | Marketing hears about features from the tracker, not from customers. | Linear, GitHub, Google Docs, Slack |
| **Competitor pricing tracker** <br>`competitor_pricing_tracker` | Price and packaging changes, dated, in a sheet, with an alert when one moves. | Firecrawl, Google Sheets, Slack |
| **SEO content gap** <br>`seo_content_gap` | Topics competitors rank for that you have no page on, as a prioritised backlog. | Firecrawl, Tavily, Google Sheets, Linear |
| **Webinar ops end to end** <br>`webinar_ops_end_to_end` | Registration, invite and reminders with no webinar tool. | Google Forms, Google Calendar, Gmail |
| **Customer story pipeline** <br>`customer_story_pipeline` | Your longest-paying happy customers become case-study drafts and a permission ask. | Stripe, Granola, Google Docs, Gmail |
| **Weekly newsletter draft** <br>`weekly_newsletter_draft` | Product news, posts and releases gathered into one draft every Friday. | Linear, GitHub, Notion, Google Docs, Gmail |
| **Event leads → enriched follow-ups** <br>`event_leads_to_enriched_follow_ups` | Booth scans become enriched leads with a follow-up waiting, same day. | Google Forms, Firecrawl, Notion, Gmail |
| **Brand mention monitor** <br>`brand_mention_monitor` | Every mention worth replying to shows up with the context to reply. | Tavily, Firecrawl, Notion, Slack |
| **Webinar transcript → blog post** <br>`webinar_transcript_to_blog_post` | An hour of talk becomes a post and a queue of social snippets. | Granola, Google Docs, Notion |
| **Site QA crawl** <br>`site_qa_crawl` | Broken links, empty pages and stale pricing get issues before a prospect finds them. | Firecrawl, Linear, Slack |
| **Launch-day pulse** <br>`launch_day_pulse` | Hourly signups and fresh bugs posted to the launch channel without anyone refreshing dashboards. | Stripe, Linear, Slack |
| **Survey → insight memo** <br>`survey_to_insight_memo` | Raw responses become a tagged sheet and a one-page memo. | Google Forms, Google Sheets, Google Docs, Slack |
| **Docs site freshness check** <br>`docs_site_freshness_check` | Public docs pages older than the code they describe get flagged. | Firecrawl, GitHub, Linear |

Every prompt takes one optional argument, `details`: the repo, team, channel, customer or date range you mean, so the agent does not have to ask. In Claude Code, put it in quotes, or only its first word arrives:

```
/mcp__marketing__what_shipped_to_blog_and_social_drafts "everything shipped since the 1st"
```

Reads run without asking. Before anything that creates, sends, changes or deletes, the prompt tells the agent to show you the call and wait.

4 of the 13 workflows need no Google or Granola credential.

## Other clients

**Claude Desktop**: install [uv](https://docs.astral.sh/uv/getting-started/installation/) if you have not, since Claude Desktop starts the server with it, then open the `.mcpb` from the [latest release](https://github.com/r28ai/marketing-ops-mcp/releases/latest). Claude asks for any keys in its own settings and keeps them in your keychain. The first start takes a few seconds longer, while uv installs it.

**VS Code** (`.vscode/mcp.json`): VS Code asks for each key the first time the server starts and stores it securely. Leave out any you stored with `login`.

```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "linear-api-key",
      "description": "Linear: Personal API key",
      "password": true
    },
    {
      "type": "promptString",
      "id": "github-token",
      "description": "GitHub: Personal access token",
      "password": true
    },
    {
      "type": "promptString",
      "id": "google-client-secret",
      "description": "Google: OAuth client secret",
      "password": true
    },
    {
      "type": "promptString",
      "id": "slack-bot-token",
      "description": "Slack: Bot token (xoxb-\u2026)",
      "password": true
    },
    {
      "type": "promptString",
      "id": "firecrawl-api-key",
      "description": "Firecrawl: API key",
      "password": true
    },
    {
      "type": "promptString",
      "id": "tavily-api-key",
      "description": "Tavily: API key",
      "password": true
    },
    {
      "type": "promptString",
      "id": "stripe-api-key",
      "description": "Stripe: Secret or restricted key",
      "password": true
    },
    {
      "type": "promptString",
      "id": "granola-api-key",
      "description": "Granola: API key",
      "password": true
    },
    {
      "type": "promptString",
      "id": "notion-api-key",
      "description": "Notion: Integration secret (ntn_\u2026)",
      "password": true
    }
  ],
  "servers": {
    "marketing": {
      "type": "stdio",
      "command": "marketing-ops-mcp",
      "env": {
        "LINEAR_API_KEY": "${input:linear-api-key}",
        "GITHUB_TOKEN": "${input:github-token}",
        "GOOGLE_CLIENT_SECRET": "${input:google-client-secret}",
        "SLACK_BOT_TOKEN": "${input:slack-bot-token}",
        "FIRECRAWL_API_KEY": "${input:firecrawl-api-key}",
        "TAVILY_API_KEY": "${input:tavily-api-key}",
        "STRIPE_API_KEY": "${input:stripe-api-key}",
        "GRANOLA_API_KEY": "${input:granola-api-key}",
        "NOTION_API_KEY": "${input:notion-api-key}",
        "GOOGLE_CLIENT_ID": ""
      }
    }
  }
}
```

**Cursor** (`.cursor/mcp.json`) starts it the same way:

```json
{
  "mcpServers": {
    "marketing": {
      "command": "marketing-ops-mcp"
    }
  }
}
```

**Codex** (`~/.codex/config.toml`) starts a turn without waiting for a server unless it is `required`, and then the agent has none of its tools. `required = true` makes the session wait for it, and `startup_readiness = "catalog"` waits for its tool list rather than just its connection:

```toml
[mcp_servers.marketing]
command = "marketing-ops-mcp"
required = true
startup_readiness = "catalog"
startup_timeout_sec = 30
```

Name the server `marketing`. A host builds each tool's name from that key, and a longer one can push a tool past the 64 characters a function name allows.

## Built with Charter

Every tool here is a [Charter](https://github.com/r28ai/charter) declaration: a Pydantic schema saying where each field goes on the wire. Charter's runtime builds the request, attaches and refreshes the credential, and trims the response before the model reads it. It runs in your process, with no proxy and no telemetry.

The 27 tool schemas come to 56,044 tokens.

The same tools work in your own agent, without MCP:

```python
from charter.adapters.openai import to_openai_tools
from charter_packs_mcp import FAMILIES

tools = FAMILIES["marketing"].tools()
definitions = to_openai_tools(tools)   # or charter.adapters.langchain
```

Need an API that isn't here? [Write a pack](https://docs.r28.ai/charter/start/coding-agents): your coding agent writes the declarations, and Charter's conformance suite checks them.

<details>
<summary>All 27 tools</summary>

- **Linear**: `linear_issues_list`, `linear_issue_create`
- **GitHub**: `github_releases_list`, `github_repos_list_commits`
- **Google Docs**: `gdocs_documents_create`
- **Slack**: `slack_chat_post_message`, `slack_chat_schedule_message`
- **Firecrawl**: `firecrawl_monitor_create`, `firecrawl_extract`, `firecrawl_map`, `firecrawl_scrape`, `firecrawl_crawl`, `firecrawl_crawl_status`
- **Google Sheets**: `gsheets_spreadsheets_values_append`, `gsheets_spreadsheets_values_update`
- **Tavily**: `tavily_search`
- **Google Forms**: `gforms_forms_create`, `gforms_forms_responses_list`
- **Google Calendar**: `gcalendar_events_insert`
- **Gmail**: `gmail_messages_send`, `gmail_drafts_create`
- **Stripe**: `stripe_customers_list`, `stripe_checkout_sessions_list`
- **Granola**: `granola_notes_list`, `granola_notes_transcript_get`
- **Notion**: `notion_data_sources_query`, `notion_pages_create`

</details>

## License

Apache 2.0.
