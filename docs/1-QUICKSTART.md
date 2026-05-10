# TiaCommander — Quick Start Guide

Get up and running in 5 minutes.

## 1. Extract

Extract the ZIP to a permanent location. Recommended:

```
C:\TiaCommander\
```

You should see `TiaCommander.exe` alongside the runtime DLLs.

## 2. TIA Portal Openness group

Your Windows user must be a member of the **Siemens TIA Openness** and **Siemens TIA Engineer** groups.
Without this, TIA Portal will refuse external connections.

If you haven't done this before, see **2-OPENNESS_GROUP.md** for step-by-step instructions.

Most TIA Portal engineers already have this configured. To check:

```powershell
whoami /groups | findstr "Siemens TIA"
```

If you see Siemens TIA groups in the output, you're good. If not, follow 2-OPENNESS_GROUP.md and **restart your PC**.

## 3. Configure your AI client

Add TiaCommander to your AI client's MCP configuration.
See **3-AI_CLIENT_SETUP.md** for detailed instructions and sample config files in the `configs/` folder.

**Tested and supported AI clients:**

- Claude Desktop
- Claude Code (CLI)
- VS Code / Visual Studio + GitHub Copilot
- Cursor
- Windsurf
- Codex CLI (OpenAI)
- Gemini CLI (Google)

## 4. Restart your AI client

Close and reopen your AI client (e.g. quit Claude Desktop from the tray icon, then relaunch).
TiaCommander starts automatically when the AI client connects.

**If quitting from the tray doesn't work:** open Task Manager (Ctrl+Alt+Delete → Task Manager), find your AI client process (e.g. Claude, Cursor, Code), and click End Task. Then relaunch the application.

## 5. Activate your license

Ask your AI assistant:

- "Open the TiaCommander manager" or "open manager"

The Manager window will open. Register with your name and email to receive a free beta license key. Enter the key and click Activate. Once activated, all tools become available.

**Note:** Some AI clients may need to load MCP tools before you can use them. Common commands:

- `/mcp` — load MCP tools (Claude Code, some CLI clients)
- `load tools` — trigger tool discovery
- Check your AI client's documentation for MCP tool activation

## 6. Start working

Open TIA Portal with a project, then ask your AI assistant:

- "Connect to TIA Portal and list all devices"
- "Show me all blocks in the PLC"
- "Create an FB for motor control with start/stop inputs"
- "Find all unused blocks in the project"
- "Export the full I/O address map"
- "Compare online and offline versions"

## Troubleshooting

**"Access denied" or connection refused:**
→ Check the Openness group membership (step 2). You must restart your PC after adding.

**AI client doesn't show TiaCommander tools:**
→ Verify the path in your config file uses double backslashes (`\\`).
→ Restart the AI client completely (not just close the window — quit from tray). If quitting from the tray doesn't work, use Task Manager (Ctrl+Alt+Delete) to end the process, then relaunch.

**TIA Portal not detected:**
→ Make sure TIA Portal is running with a project open before connecting.
→ Try: "Connect to TIA Portal" in your AI assistant.

## Need help?

- Open the TiaCommander Manager: ask your AI assistant "open manager"
- Visit [tiacommander.com](https://tiacommander.com)
- File an issue on [GitHub](https://github.com/a4webdev/tiacommander-mcp/issues)
