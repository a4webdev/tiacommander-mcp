# TiaCommander User Guide

This guide covers day-to-day usage of TiaCommander — the Manager window,
terminal commands, auto-updates, and how everything fits together with
your AI assistant.

---

## Getting Started

TiaCommander runs as an MCP (Model Context Protocol) server. Your AI
assistant (Claude Desktop, Cursor, Windsurf, VS Code Copilot, etc.)
launches it automatically when you start a conversation. You don't need
to start TiaCommander manually.

To begin, ask your AI assistant:

> "Open the TiaCommander Manager"

This opens the Manager GUI where you can register, activate your license,
configure paths, and monitor the server.

---

## The Manager Window

The Manager is the central dashboard for TiaCommander. It has several tabs:

### Terminal Tab

The terminal shows server activity — license checks, telemetry status,
update notifications, and tool call logs. You can type commands directly
into the input field at the bottom.

### MCP Config Tab

Shows configuration snippets for connecting TiaCommander to various AI
clients. Select your client from the dropdown, copy the JSON snippet,
and paste it into your client's MCP configuration file.

### Paths Tab

Configure default folders for:
- **Projects root** — where TIA Portal projects are stored
- **Archives root** — where project archives (.zap files) are saved
- **Exports root** — where hardware maps, alarm exports, and other
  generated files are placed
- **Libraries root** — where global libraries (.al19) are stored

Setting these paths means you don't have to specify directories every
time you create, open, archive, or export.

### Help Tab

Displays the TiaCommander documentation (online when available,
offline fallback when not connected).

### GitHub Tab

Shows releases, star count, and a feedback form for reporting issues
or submitting feature requests.

---

## Terminal Commands

Type these commands in the Manager terminal input field:

| Command | What it does |
|---------|-------------|
| `update` | Check for new versions and install if available |
| `status` | Show whether the MCP server process is running and which AI client is connected |
| `stop` | Kill the headless MCP server process (frees the mutex so another client can connect) |
| `clear` | Clear the terminal output |
| `help` | Show the list of available commands |

---

## Auto-Update

TiaCommander checks for updates automatically when the Manager opens.
If a newer version is available, you'll see a message like:

```
Update available: v2.25.1 → v2.25.4. Type 'update' to install.
```

### How to update

1. Type `update` in the terminal
2. Review the release notes and download size
3. Type `y` to confirm

### What happens during an update

The update process is fully automatic and safe:

1. **Download** — the new version is downloaded as a ZIP file from
   GitHub to your system's temp folder
2. **Close** — the Manager window closes
3. **Stop server** — the MCP server process is stopped (your AI
   assistant will temporarily lose the connection)
4. **Backup** — your current installation files are moved to a
   `.prev/` folder inside the TiaCommander directory. Your **data
   and logs are never touched** — they are preserved across updates
5. **Extract** — the new version is extracted from the ZIP into
   the TiaCommander directory
6. **Relaunch** — the Manager reopens automatically with the new
   version and shows a success message
7. **Clean up** — the downloaded ZIP is deleted from your temp folder

### After the update

You need to **restart your AI assistant** (quit and relaunch Claude
Desktop, Cursor, etc.) for it to connect to the new version of the
MCP server. The Manager will remind you:

```
Updated successfully: v2.25.1 → v2.25.4
Please restart your AI assistant (Claude Desktop, Cursor, etc.) to use the new version.
```

### Is it safe?

Yes. The update process includes several safety measures:

- **Automatic backup** — your previous version is saved in `.prev/`.
  If anything goes wrong, the old files are still there
- **Data preservation** — the `data/` folder (containing your license,
  settings, and usage statistics) and `logs/` folder are never modified
  during an update
- **Rollback on failure** — if the extraction fails (corrupt download,
  disk full, etc.), the updater automatically restores your previous
  version from the backup
- **Verification** — after extraction, the updater checks that the new
  `TiaCommander.exe` exists and is not empty before launching it
- **No admin required** — the update runs entirely in user space. No
  UAC prompts, no system changes, no registry modifications

### Manual recovery

In the unlikely event that both the update and automatic rollback fail,
you can manually recover:

1. Navigate to your TiaCommander folder (default: `C:\TiaCommander\`)
2. Copy the contents of the `.prev\` subfolder back to the main folder
3. Restart your AI assistant

---

## License & Registration

### First-time setup

When you open the Manager for the first time:

1. Enter your name, surname, and email
2. Click **Register** — you'll receive a license key by email
3. Enter the license key and click **Activate**
4. Your license is validated against the server and stored locally

### License validation

TiaCommander validates your license on every startup. If the server
is unreachable (no internet), it falls back to a cached validation
that remains valid for up to 72 hours. This means you can work
offline without interruption.

### Resend license key

If you lost your license key, enter your email in the registration
form and click **Resend Key**. The key will be sent to the email
address associated with your account.

---

## Architecture: How It Works

TiaCommander runs as two processes:

1. **Headless MCP server** — launched by your AI assistant via stdio
   pipes. This is the process that handles tool calls (read blocks,
   create tags, export hardware, etc.). It runs silently in the
   background.

2. **Manager GUI** — the window you interact with. It shares the same
   executable but runs in GUI mode. It can monitor the headless server,
   check for updates, and manage configuration.

Both processes use a mutex to coordinate. Only one headless MCP server
can run at a time. If you need to switch AI clients, use the `stop`
command in the Manager to free the mutex, then start a conversation
in your other AI client.

---

## Troubleshooting

### "No running MCP server process found"

This means the AI assistant hasn't started TiaCommander yet. Start a
conversation in your AI client and use any TiaCommander tool — the
server will launch automatically.

### AI assistant says TiaCommander tools are unavailable

Restart your AI assistant. The MCP connection may have been lost
(e.g., after an update or if the server process was killed).

### Update fails with "404 Not Found"

This can happen if the download URL has changed. Try running `update`
again — the server will fetch the latest release information.

### TIA Portal access dialog appears

When TiaCommander connects to TIA Portal for the first time after a
rebuild, Windows may show a security dialog asking if you want to
allow access. Click **Yes** and **Yes** at the UAC prompt. To suppress
this permanently, run the publish script as Administrator.
