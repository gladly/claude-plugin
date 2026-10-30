# Gladly plugin for Claude

Connect Claude to your Gladly organization so you can ask about and improve your Gladly Agents, Guides, Workflows, and conversations in plain English.

## Before you start

- **Your role:** you need to be a team manager, system administrator, or compliance administrator in your Gladly organization.
- **Access:** Gladly needs to have turned on the Gladly MCP for your organization. If it isn't on yet, contact your Gladly account team.
- **Your Gladly address:** the address you use to sign in to Gladly, for example `acme.us-1.gladly.com`. Copy it from your browser's address bar while signed in.

## Claude Desktop, claude.ai, and Cowork

1. Go to **Customize > Plugins** and select **Add > Add marketplace**.
2. Enter `gladly/claude-plugin`, keep **Sync automatically** on, and select **Sync**.
3. Find **Gladly** under **Discover** and select **Add**.
4. In a new chat, run `/gladly-setup` (`/gladly:gladly-setup` in Cowork), or ask Claude to "set up Gladly."
5. Enter your Gladly address when Claude asks. Claude gives you the connector to add and the steps for your app.
6. Click **Connect**, sign in to Gladly, review the consent screen, and click **Allow access**.
7. Claude checks which organization you're connected to. Confirm it's yours.

If you don't see **Add marketplace** or **Add custom connector**, your company's Claude Owner controls these. Claude writes a message you can send them.

## Claude Code (terminal or IDE)

1. Run `/plugin marketplace add gladly/claude-plugin`.
2. Run `/plugin install gladly@gladly`.
3. Run `/gladly:gladly-setup` and enter your Gladly address. Claude adds the connector for you.
4. Run `/reload-plugins`, then `/mcp`. Choose the Gladly server and sign in to Gladly.
5. Run `/gladly:gladly-setup` again to confirm your organization.

To get new plugin versions on their own, turn on auto-update for the marketplace under `/plugin`.

## The Code tab in Claude Desktop

The Code tab can't sign in to a connector on its own. Connect Gladly using the **Claude Desktop** steps first, then start a new session in the Code tab.

## Conductor, Orca, and other apps that run Claude Code

These apps use Claude Code, so they can use the Gladly plugin. Their chat box can't open the Gladly sign-in, so set up from a terminal.

1. In a terminal, add the plugin:

   ```bash
   claude plugin marketplace add gladly/claude-plugin
   claude plugin install gladly@gladly
   ```

2. Run `claude`, then `/gladly:gladly-setup`, and enter your Gladly address. Claude adds the connector for you.
3. Run `/reload-plugins`, then `/mcp`. Choose the Gladly server, select **Authenticate**, and sign in to Gladly.
4. Close the terminal and restart your app's session. In Conductor, click **Refresh** above the chat or send `/restart`.
5. Ask Claude which Gladly organization it's connected to.

If your app can't load plugins, add the connector directly instead of steps 1 and 2. Use your connector address, described under **Other MCP clients**:

```bash
claude mcp add --transport http --scope user gladly https://mcp.us-1.gladly.com/<your-organization>/mcp
```

## Other MCP clients

Add a remote MCP server with your connector address. Choose HTTP (streamable HTTP) and OAuth when asked. The app opens Gladly for you to sign in.

Your connector address uses the first part of your Gladly address. For `acme.us-1.gladly.com` or `acme.gladly.com`, that's `acme`:

```txt
https://mcp.us-1.gladly.com/acme/mcp
```

## Troubleshooting

| What you see | What to do |
| --- | --- |
| "The organization isn't recognized" | Check your Gladly address for typos and run setup again. |
| "Only team managers can allow an application to use their Gladly access" | Ask a team manager, system administrator, or compliance administrator to connect, or ask a Gladly administrator at your company for one of those roles. |
| "Connecting MCP clients isn't available for this organization yet" | Contact your Gladly account team to turn on access. |
| "MCP is turned off for this organization" | Ask an administrator in your Gladly organization to turn it on. |
| You're sent to the Gladly sign-in page | Sign in. The connection continues. |
| You're asked to reconnect | The connection lapses after a few days without use, or when your Gladly sign-in expires. Click **Connect** again, or run `/mcp` in Claude Code. |

Still stuck? Contact your Gladly account team.

## License

Apache License 2.0. See [LICENSE.md](LICENSE.md).
