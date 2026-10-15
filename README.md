# Tukey MCP

Tukey helps an AI assistant recommend evidence-backed video opportunities tailored to the creator's active YouTube channel.

This repository is a public technical quickstart for connecting Tukey to an AI assistant over the Model Context Protocol (MCP). The canonical, maintained setup and support guide is **https://tukey.ai/support**. Where this document and that page differ, the support page is authoritative.

Last verified: 2026-10-15

## Current availability

- **YouTube only.** Tukey works with connected YouTube channels. Instagram and TikTok are not currently supported.
- **Existing eligible Tukey account required.** You need an existing Tukey account that currently has access, one active connected YouTube channel, and a completed channel profile. If the account has access but the channel or profile is not ready, authorization may complete and Tukey truthfully reports that the channel, profile or opportunities are not ready yet. If the account does not have access, authorization cannot complete and no Tukey account or channel data is returned. The public server address does not provide open signup or self-service access.
- Tukey makes no guarantee of views, performance, revenue, ranking, growth or virality. It recommends; the creator decides.

## Canonical endpoint

```
https://www.tukey.ai/mcp
```

Transport: streamable HTTP (remote MCP). Authentication: OAuth 2.1 authorization code with PKCE, with automatic (dynamic) client registration. Every request must carry an access token issued for this endpoint; there is no anonymous access.

## Prerequisites

1. A Tukey account that currently has access.
2. One active connected YouTube channel in Tukey (Connections page).
3. A completed Tukey channel profile. Profiling and the first batch of opportunities take time after a channel is connected; if the account has access but the channel or profile is not ready, authorization may complete and Tukey truthfully reports that the channel, profile or opportunities are not ready yet.
   If the account does not have access, authorization cannot complete and no Tukey account or channel data is returned.
4. An assistant and plan that support custom remote MCP servers (see below).

## ChatGPT setup (plan and workspace qualified)

Tukey is not yet listed in the ChatGPT Plugin Directory, so it is added as a custom app. Menu labels may vary while the feature is in beta, and full MCP support (including write actions) is currently subject to ChatGPT plan and workspace controls.

- **Business, Enterprise and Edu.** A workspace Admin or Owner, or a developer they authorize:
  1. enables Developer mode;
  2. opens Settings (or Workspace settings) > Apps > Create;
  3. enters `https://www.tukey.ai/mcp` as the server URL;
  4. selects OAuth and completes the authorization (sign in to Tukey, approve the consent page);
  5. lets ChatGPT scan the tools;
  6. creates and enables the app.
- **Pro.** OpenAI currently limits custom MCP use on Pro to eligible read/fetch capabilities in Developer mode. Tukey has not separately canary-tested the Pro surface. Do not rely on Tukey's two action tools being available on Pro.
- **Other plans.** If the plan does not offer custom MCP apps, Tukey cannot be connected to ChatGPT yet.

## Claude setup (individual versus organization)

Tukey is not yet listed in the Claude Connectors Directory, so it is added as a custom connector.

- **Pro and Max (individual).** Customize > Connectors > + Add > Add custom connector. Enter `https://www.tukey.ai/mcp` and continue with the OAuth settings Claude detects.
- **Team and Enterprise (organization).** An Owner, or another authorized role, first adds the connector under Organization settings > Connectors with the same endpoint. Members then connect it under Customize > Connectors.

Claude's own "disconnect" does not necessarily end Tukey's authorization; see *Disconnect and revocation*.

## OAuth and consent

1. The assistant registers itself with Tukey automatically (dynamic client registration) and starts an OAuth authorization-code flow with PKCE.
2. You are sent to Tukey to sign in (email and password, or Google).
3. Tukey's consent page names the assistant that is asking and shows the address that will receive access (for example `chatgpt.com` or `claude.ai`). Check that it matches the assistant you are using.
4. The page lists what the assistant will be able to do. Reading needs the `tukey:read` permission; the two action tools additionally need `tukey:write`. Choose **Allow** to connect or **Cancel** to stop. Nothing is connected until you choose Allow.
5. Tukey issues the assistant short-lived access tokens (valid for at most one hour) and, when offline access was granted, a refresh token so it can stay connected until you disconnect it.

Tukey never shares your YouTube analytics or private channel insights with an assistant, and Tukey never publishes to or modifies your YouTube channel.

## Tools

Exactly six tools are exposed.

**Read tools (do not change your account)**

| Tool | What it returns |
| --- | --- |
| `get_profile` | Which Tukey account is connected (its name and email address), so the assistant can tell accounts apart. No channel data. |
| `get_channel_profile` | Tukey's profile of the active YouTube channel: what it is about, its formats, voice, hooks, audience, and subscriber and video counts. |
| `list_video_opportunities` | The current video opportunities for the channel, each with title, premise, angle, hook, why it fits, why now and how much evidence backs it. Can include saved ideas. |
| `get_opportunity_evidence` | The dated public sources behind one opportunity. |

**Action tools (change your account; use only after an explicit user request)**

| Tool | What it does |
| --- | --- |
| `rate_video_opportunity` | Records a keep, weak or reject rating on one opportunity, exactly as if you had rated it in Tukey. |
| `start_video_opportunity` | Starts one opportunity as a Tukey video workspace and saves it. Starting the same opportunity again returns the same workspace and never creates a second one. |

## Example prompts

- "What should I make next on my channel?"
- "List my current Tukey video opportunities."
- "Why does the first opportunity fit my channel?"
- "Show me the evidence behind the second opportunity."
- "Mark the third opportunity as weak in Tukey."
- "Start the first opportunity in Tukey."

The last two are explicit requests for the action tools; an assistant should not rate or start an opportunity you did not ask it to.

## Disconnect and revocation

- **Tukey Connections > Disconnect** is the authoritative Tukey-side revocation control. Open Connections in Tukey, find the assistant under Connected agents, and choose Disconnect.
- Tukey deletes the assistant's authorization and stored tokens and issues it no new or refreshed tokens.
- Tukey's endpoint is designed to refuse an already-issued access token on its next protected request.
- In any event, access tokens expire within one hour of being issued.
- Disconnecting from inside ChatGPT also ends Tukey's authorization. Removing the connector inside Claude does not by itself end Tukey's authorization; use Tukey's Connections page as well.

## Troubleshooting

- **Cannot find the setup menu.** Labels vary by plan and while these features are in beta. In ChatGPT Business, Enterprise or Edu a workspace Admin or Owner must create the app; in Claude Team or Enterprise an Owner adds the connector first.
- **Action tools missing.** ChatGPT capabilities depend on the current plan and workspace controls. Pro may be limited to eligible read/fetch capabilities, and Tukey has not separately canary-tested that surface. Rating and starting always remain available inside Tukey itself.
- **Could not reach Tukey or sign-in failed.** Check that the server address is exactly `https://www.tukey.ai/mcp`, then connect again. If you had already approved consent, remove the connection in the assistant, disconnect it in Tukey's Connections page, and connect again.
- **Consent page says the account has no access.** The Tukey account is not currently eligible. Contact support@tukey.ai.
- **Tukey says there is no channel.** Connect a YouTube channel in Tukey and make it the active channel. Assistants only see the active channel.
- **No profile or no ideas yet.** Profiling and the first opportunities take time after a channel is connected.
- **A rejected idea is missing.** Tukey withholds topics you declined for a while. This is expected.
- **The assistant answered a question about view counts.** Tukey does not provide per-video view counts or performance analytics through MCP; use YouTube Studio.

## Current limitations

- YouTube only.
- Access-controlled Tukey accounts; no open signup through MCP.
- No YouTube analytics through MCP.
- No Instagram or TikTok support yet.
- No guaranteed outcome of any kind.

## Links

- Support and setup guide (canonical): https://tukey.ai/support
- Privacy Policy: https://tukey.ai/privacy-policy
- Terms of Service: https://tukey.ai/terms-of-service
- Official MCP Registry entry (published, active, version 0.1.0): https://registry.modelcontextprotocol.io/v0.1/servers/ai.tukey%2Ftukey/versions/0.1.0

## Maintenance

https://tukey.ai/support is the canonical guide. This document must be updated whenever the endpoint, the tool inventory, the supported clients or plans, or availability changes, and the "Last verified" date above must be refreshed at the same time.
