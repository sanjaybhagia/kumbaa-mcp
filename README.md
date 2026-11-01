<p align="center"><img src="assets/logo-400.png" width="120" alt="Kumbaa"></p>

# Kumbaa MCP server

**Your family archive in any AI assistant.** [Kumbaa](https://kumbaa.app) is a private
website for one family: its family tree, the life stories of the people in it, and its old
photographs, seen only by the relatives the family invites. Connect it to Claude, ChatGPT or
any app that speaks the [Model Context Protocol](https://modelcontextprotocol.io) to ask
about your family, look at its photos, and, if you look after the archive, add people,
link them into the tree, caption and tag photos, and invite relatives.

```
https://kumbaa.app/mcp
```

It's a hosted, remote server (Streamable HTTP). There's nothing to install and no API key.
The first time a tool is used, you sign in with the same Kumbaa account you use on your
family's website and approve the connection. One connection reaches every family archive
you belong to, with the role you have in each.

> This repository holds connection instructions and configuration only. The Kumbaa service
> and its server code are not here.

## Before you connect

You need a Kumbaa account that's already in a family archive. Sign in once on your family's
website (`<family>.kumbaa.app`) with the email address you were invited with, or start an
archive at [kumbaa.app](https://kumbaa.app). The assistant then acts as that account. If
you haven't done this, every tool tells you to sign in on the website first.

## Try asking

- "Who were my great-grandparents on my father's side?"
- "Tell me about Nani: when was she born, and who were her children?"
- "Find the photos from the 1965 wedding, and who's in them."
- "Show me the photo of the old family house."
- "Add my cousin Priya, born 1990, as Ravi and Meena's daughter." *(curators)*
- "Caption these three photos and put them in the Diwali album." *(curators)*
- "Invite asha@example.com to the family as a viewer." *(curators)*

## Connect it

| App | How |
|---|---|
| **Claude** (web, desktop, mobile) | Settings → Connectors → Add custom connector → `https://kumbaa.app/mcp`. Step by step: [kumbaa.app/claude](https://kumbaa.app/claude). |
| **ChatGPT** | Add it as an app with the URL above (Developer mode, on Plus, Pro, Business, Enterprise and Edu). Step by step: [kumbaa.app/chatgpt](https://kumbaa.app/chatgpt). |
| **Claude Code** | `claude mcp add --transport http kumbaa https://kumbaa.app/mcp`, then `/mcp` to sign in. |
| **VS Code** (Copilot) | Command palette → *MCP: Add Server* → HTTP → `https://kumbaa.app/mcp`, or copy [clients/vscode-mcp.json](clients/vscode-mcp.json) to `.vscode/mcp.json`. |
| **Gemini CLI** | `gemini extensions install https://github.com/sanjaybhagia/kumbaa-mcp`, then `/mcp auth kumbaa`. |
| **Codex CLI** | Add [clients/codex-config.toml](clients/codex-config.toml) to `~/.codex/config.toml`, then `codex mcp login kumbaa`. |
| **Cursor** | Settings → MCP → Add server, or copy [mcp.json](mcp.json) to `~/.cursor/mcp.json`. See [Auth details](#auth-details). |
| **Windsurf** | Add [clients/windsurf-mcp_config.json](clients/windsurf-mcp_config.json) to `~/.codeium/windsurf/mcp_config.json`. |
| **Anything else** | Point it at `https://kumbaa.app/mcp`. For apps that only run local (stdio) servers, see [llms-install.md](llms-install.md). |

## Tools

Everyone in a family can use the reading tools. The tools that change things work for the
family's owner and curators; a viewer who tries one is told that only curators can.

| Tool | What it does |
|---|---|
| `list_families` | The family archives you belong to, your role in each, and which one is current. |
| `switch_family` | Make another of your family archives the current one. |
| `list_people` | Everyone in a family tree, or those matching a name, with dates, parents, partners and children. |
| `get_person` | One person's profile: names, dates, places, story, family links and photos. |
| `list_albums` | A family's photo albums, with years and photo counts. |
| `get_album` | One album and its photos, with who is in each. |
| `find_photos` | Find photos by caption, place, person or album, or those still missing a detail. |
| `view_photo` | Look at one photo, with its caption, year, place and the people in it. |
| `add_person` | Add someone to the family tree. |
| `update_person` | Change someone's details. |
| `remove_person` | Remove someone from the tree (their photos stay). Asks first. |
| `link_people` | Add or remove a parent, child, spouse or sibling link. Removing asks first. |
| `update_marriage` | A marriage's date, place and story. |
| `set_tree_start` | The person the family tree opens on. |
| `set_portrait` | Use a photo as someone's portrait. |
| `update_photo` | Change a photo's caption, year, place, album or the people in it, or share it with the family. |
| `update_photos` | The same change to many photos at once. |
| `upload_photo` | Add a photo from an image URL or file. It waits for a curator unless shared straight away. |
| `delete_photo` | Delete a photo for good. Asks first. |
| `save_album` | Create an album, or change one. |
| `delete_album` | Delete an album; its photos stay. Asks first. |
| `list_members` | Who is in the family archive, and who has been invited. |
| `invite_people` | Invite relatives by email, as viewers or curators. |
| `revoke_invitation` | Take back an invitation nobody has used. |
| `change_member_role` | Make a member a curator or a viewer. |
| `remove_member` | Take someone's access away. Asks first. |
| `rename_family` | Change the family archive's name. |

Every tool has a title and read-only or destructive annotations. Anything that removes
people, photos, albums, family links or members needs the person's clear yes, and the server
asks the assistant to get it first. Handing over ownership, deleting a family archive,
backups and face detection stay on the website.

## Privacy

Each call is checked against your membership of that family at that moment, exactly as on
the website. A family you aren't in looks the same as one that doesn't exist. See the
[privacy policy](https://kumbaa.app/privacy).

## Plans

A family archive is free for 14 days, then US$6.99 a month or US$69 a year, per family.
Everyone the family invites joins free. Connecting an assistant costs nothing extra. See
[kumbaa.app/pricing](https://kumbaa.app/pricing).

## Auth details

- OAuth 2.1 with PKCE (S256), public clients. The authorization server is
  `https://clerk.kumbaa.app` (Clerk).
- Clients identify themselves with a client ID metadata document (CIMD,
  `client_id_metadata_document_supported: true`). Claude, Claude Code and ChatGPT use it;
  VS Code and Gemini CLI say they support it too. Dynamic client registration is on as
  well (`registration_endpoint: https://clerk.kumbaa.app/oauth/register`), for Cursor,
  `mcp-remote` and other apps that only use it.
- Discovery: an unauthenticated request gets `401` with
  `WWW-Authenticate: Bearer resource_metadata="https://kumbaa.app/.well-known/oauth-protected-resource/mcp"`.
  Also `https://kumbaa.app/.well-known/oauth-protected-resource` and
  `https://kumbaa.app/.well-known/oauth-authorization-server`.
- Access tokens are issued for exactly `https://kumbaa.app/mcp` (the `aud` claim). A
  website session token, or a token for any other address, is refused.
- Official MCP Registry name: `app.kumbaa/kumbaa` ([server.json](server.json)).

## Links

[kumbaa.app](https://kumbaa.app) · [Features](https://kumbaa.app/features) ·
[Claude](https://kumbaa.app/claude) · [ChatGPT](https://kumbaa.app/chatgpt) ·
[Pricing](https://kumbaa.app/pricing) · [Privacy](https://kumbaa.app/privacy) ·
[Terms](https://kumbaa.app/terms) · [Support](https://kumbaa.app/support) ·
support@kumbaa.app

Made in Sydney by Arigato Consulting Pty Ltd. The files in this repository are MIT licensed;
see [LICENSE](LICENSE).
