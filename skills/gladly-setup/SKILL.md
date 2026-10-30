---
name: gladly-setup
description: Connect Claude to the user's Gladly organization. Asks for their Gladly address, builds their Gladly connector URL, adds the connector (or walks them or their Claude Owner through adding it), and checks the connection. Use when the user runs /gladly-setup, asks to "set up Gladly", "connect Gladly", "add the Gladly connector", or can't get the Gladly connector to work.
---

# Set up Gladly

Connect Claude to one Gladly organization through the Gladly connector. The connector URL is different for every organization, so build it from the user's Gladly address. Keep messages to the user short and in plain English. Do one step at a time and wait for the user where a step needs them.

When this skill asks the user to run setup again, name the command for their app: `/gladly-setup`, or `/gladly:gladly-setup` in Claude Code and Cowork.

If the Gladly connector's ping tool is available, skip to step 4.

Before step 1, tell the user in one or two sentences: only a team manager, system administrator or compliance administrator in their Gladly organization can connect, and Gladly must have enabled their organization for the MCP beta.

## 1. Get the Gladly address

Ask: "What address do you use to sign in to Gladly? For example, `acme.us-1.gladly.com`."

- Accept a bare host or a full URL. Take the host, lowercase it, and drop any port or path.
- The host must be exactly `<slug>.us-1.gladly.com` or `<slug>.gladly.com` (production), or `<slug>.us-uat.gladly.qa` (UAT), where `<slug>` is one label of letters, digits and hyphens that doesn't start or end with a hyphen, and isn't `mcp` or `www`. Older organizations sign in on their own domain, with no site in it: `acme.gladly.com` rather than `acme.us-1.gladly.com`.
- The slug is the first label: `acme.us-1.gladly.com` gives `acme`.
- Refuse anything else. Say: "That doesn't look like a Gladly address. It should end in `.us-1.gladly.com` or `.gladly.com`, or `.us-uat.gladly.qa` for UAT. You can copy it from your browser's address bar when you're signed in to Gladly."

## 2. Build the connector URLs

- Production: `https://mcp.us-1.gladly.com/<slug>/mcp`
- UAT: `https://mcp.us-uat.gladly.qa/<slug>/mcp`

A `us-1.gladly.com` or `gladly.com` address gets the production URL, plus the UAT one only if they ask. A `us-uat.gladly.qa` address gets the UAT URL only. Use only the URLs built here in step 3.

Show the URL in a code block so it's easy to copy.

## 3. Add the connector

Work out which app the user is in. If you can run shell commands, run `echo "$CLAUDE_CODE_ENTRYPOINT"` on its own.

- `cli` or `claude-vscode`: it's Claude Code in a terminal or IDE. Follow **Claude Code** below.
- Any other value, or no shell at all: it's the Code tab in Claude Desktop, a Claude Desktop chat, claude.ai or Cowork. Follow **Claude Desktop, claude.ai and Cowork** below. The Code tab in Claude Desktop can run `claude mcp`, but it can't sign in with `/mcp`, so don't use `claude mcp add` there.
- Ask if you can't tell.

**Claude Code.** Use the name `gladly` for production and `gladly-uat` for UAT. First run `claude mcp get <name> 2>&1 || true`. If it says there's no server with that name, that's expected on a first run. If it exists with a different URL, tell the user which URL is there and that it'll be replaced, and only once they agree, run `claude mcp remove <name> --scope <scope it shows>`. If it exists with the same URL, skip the add. Otherwise run:

```bash
claude mcp add --transport http --scope user <name> <url>
```

The running session doesn't see the new server yet. Ask the user to run `/reload-plugins` to load it, then run `/mcp`, choose the Gladly server and sign in. Then run setup again to verify.

If you added a server with `claude mcp add` and then learn the user isn't in Claude Code in a terminal or IDE, tell them it's there, recommend removing it, and only once they agree, run `claude mcp remove <name> --scope user`. Then follow **Claude Desktop, claude.ai and Cowork** below.

**Claude Desktop, claude.ai and Cowork.** Give these steps:

1. Go to **Customize > Connectors**. If a Gladly connector is already there, for example one your Claude Owner added, click **Connect** on it and go to the sign-in step below. Otherwise click **+ Add**, then **Add custom connector**.
2. Name it "Gladly (Prod)", paste the URL and click **Continue**.
3. Keep the detected authentication settings and click **Continue**.
4. Leave **Authentication**, **OAuth client** and **Request headers** as they are, and click **Add**.
5. Click **Connect**. Your browser opens to Gladly.
6. Sign in if asked, review the consent screen and click **Allow access**.
7. Back in Claude, Gladly shows as **Connected**. In this chat, click **+**, then **Connectors**, and turn Gladly on. If it isn't listed, start a new chat. Then run setup again to check the connection.

For UAT, name it "Gladly (UAT)" and use the UAT URL.

**The organization restricts connectors.** If the user can't see **Add custom connector**, or Claude says their organization manages connectors, write a message they can send to their Claude Owner:

> Hi, could you add the Gladly connector for our Claude organization? Go to **Organization settings > Connectors**, add a custom connector named "<connector-name>" with the URL `<url>`. After that, each of us connects it and signs in with our own Gladly account. Thanks!

Fill in the name and URL of the connector they're adding, as in the steps above. Tell the user that once the Owner has added it, they find it under **Customize > Connectors**, click **Connect**, and then run setup again in a new chat.

## 4. Verify

Once the user is connected, call the Gladly connector's ping tool. It returns `user`, `scope` and `targetOrg`. `targetOrg` has `resolved: true` and the organization's `name`, or `resolved: false` and a `reason`. If more than one Gladly connector is available, call the one for the address from step 1. If you skipped here, or can't tell which one that is, ask the user which to check.

- If `targetOrg.resolved` is `true`, tell the user they're connected to `targetOrg.name` and ask them to confirm it's their organization. If it isn't, ask them to remove the connector (in Claude Code, `claude mcp remove <name> --scope <the scope claude mcp get shows>`), go back to step 1 and check the address.
- If `targetOrg.resolved` is `false`, say they're connected but Gladly couldn't look up the organization's name just now, and call the tool once more. If it still fails, say they're connected but setup couldn't confirm the organization, and suggest running setup again later to check. Don't say they're set up.
- If `targetOrg` is missing, or has neither, say they're connected but setup couldn't confirm the organization, and suggest they contact their Gladly account team. Don't say they're set up.
- If the tool isn't available, ask whether they've added and connected the connector. If not, go back to step 3. If they have, in Claude Code ask them to run `/reload-plugins`, or restart with `claude --continue` if that doesn't help. In the Code tab in Claude Desktop, ask them to start a new session. Elsewhere, ask them to turn Gladly on under **+ > Connectors** in this chat, or start a new chat. Then run setup again.

Once they confirm the organization, say they're set up, and suggest a first question to ask about their Gladly.

## 5. Troubleshooting

- **Sign-in fails, saying the organization isn't recognized:** Gladly doesn't recognize the organization in the URL. Check the address for typos, and rebuild the URL.
- **"Only team managers can allow an application to use their Gladly access":** only a team manager, system administrator or compliance administrator in the Gladly organization can connect. Ask someone with one of those roles to connect, or a Gladly administrator in their company to give them one.
- **"Connecting MCP clients isn't available for this organization yet":** Gladly hasn't enabled their organization for the MCP beta. They need to ask their Gladly account team for access.
- **"MCP is turned off for this organization":** an administrator in their Gladly organization needs to turn MCP on.
- **Sent to the Gladly sign-in page:** they're signed out of Gladly. Sign in, and the connection continues.
- **Asked to reconnect later:** the connection lapses after a few days without use, or sooner if their Gladly sign-in expires. Click **Connect** again.
- Anything else: suggest they contact their Gladly account team.
