# TiaCommander — Quick Start Guide

Get up and running in 5 minutes.

## Prerequisites

> - **Siemens TIA Portal V15.1 or later** (Professional edition) — must be installed on your machine
> - Openness API enabled during TIA Portal installation
> - Windows 10/11 (64-bit)
>
> TiaCommander loads Siemens Openness libraries directly from your TIA Portal installation at runtime. It **will not start** without TIA Portal.

## 1. Download

Grab the latest portable `.zip` from [GitHub Releases](https://github.com/a4webdev/tiacommander-mcp/releases/latest).

## 2. Extract

Extract the ZIP to a permanent location. Recommended location:

```
C:\TiaCommander\
```

You should see `TiaCommander.exe` alongside the runtime DLLs.

## 3. TIA Portal Openness group

Your Windows user must be a member of the **Siemens TIA Openness** and **Siemens TIA Engineer** groups.
Without this, TIA Portal will refuse external connections. 

This is a one-time setup. Most TIA Portal engineers already have this configured.

#### Check if you already have access

Open PowerShell and run:

```powershell
whoami /groups | findstr "Siemens TIA"
```

If you see **Siemens TIA Openness** or **Siemens TIA Engineer** in the output (see sample below), you're likely set up. 

![Checking Siemens TIA group membership](https://tiacommander.com/img/github/1-Quickstart/quickstart_openness_ss1.png)

If neither appears, follow the steps below:

#### Method 1: PowerShell (recommended)

1. Open **PowerShell as Administrator** (right-click → Run as administrator)

2. Run:
   ```powershell
   Add-LocalGroupMember -Group "Siemens TIA Openness" -Member "$env:USERNAME"
   Add-LocalGroupMember -Group "Siemens TIA Engineer" -Member "$env:USERNAME"
   ```

3. Verify:
   ```powershell
   Get-LocalGroupMember -Group "Siemens TIA Openness"
   Get-LocalGroupMember -Group "Siemens TIA Engineer"
   ```

![Verifying group membership in PowerShell](https://tiacommander.com/img/github/1-Quickstart/quickstart_openness_ss2.png)

4. **Restart your PC** (mandatory)

#### Method 2: Computer Management (GUI)

1. Right-click **Start** → **Computer Management**
2. Navigate to **Local Users and Groups → Groups**
3. Double-click **Siemens TIA Openness**, click **Add**, type your Windows username, click **Check Names**, then **OK**
4. Repeat for **Siemens TIA Engineer**
5. **Restart your PC** (mandatory)

![Adding user to Siemens TIA groups via Computer Management](https://tiacommander.com/img/github/1-Quickstart/quickstart_openness_ss3.png)

#### Important: restart required

After adding yourself to the group, you **must restart your computer**. Logging out and back in is not sufficient — Windows caches group memberships at boot time.

After restarting, verify with:

```powershell
whoami /groups | findstr "Siemens TIA"
```

You should see something like this:

![Siemens TIA groups confirmed](https://tiacommander.com/img/github/1-Quickstart/quickstart_openness_ss1.png)

#### Group not found?

If the "Siemens TIA Openness" group doesn't exist on your machine, TIA Portal may not be installed, or the Openness API component was not selected during installation. Reinstall TIA Portal and ensure the **Openness** option is checked.

## 4. Configure your AI client

Add TiaCommander to your AI client's MCP configuration.

TiaCommander works with any AI client that supports the Model Context Protocol (MCP) over stdio. Below are configuration instructions for all tested AI Clients.

Sample config files are in the `configs/` folder — copy the one for your client and adjust the path if needed.

The default install path is `C:\TiaCommander\TiaCommander.exe`. If you extracted to a different location, update the path accordingly. Always use double backslashes (`\\`) in JSON paths.

Expand the section for your AI client below:

---

<details>
<summary><strong>Claude Desktop</strong></summary>

**Config file:** `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "tiacommander": {
      "command": "C:\\TiaCommander\\TiaCommander.exe"
    }
  }
}
```
Open the config file with Notepad++ (or any text editor):

![Opening claude_desktop_config.json in Notepad++](https://tiacommander.com/img/github/1-Quickstart/quickstart_claude-desktop_ss1.png)

Add the MCP server entry and save the file.

![Adding TiaCommander MCP entry to config](https://tiacommander.com/img/github/1-Quickstart/quickstart_claude-desktop_ss2.png)

After saving, use Task Manager (Ctrl+Alt+Delete) to end the Claude Desktop process, then relaunch.

Verify TiaCommander is loaded — you should see it listed under the Developer section in Claude Desktop:

![TiaCommander tools loaded in Claude Desktop](https://tiacommander.com/img/github/1-Quickstart/quickstart_claude-desktop_ss3.png)

</details>

<details>
<summary><strong>Claude Code (CLI)</strong></summary>

Run this command in your terminal:

```
claude mcp add --transport stdio --scope user tiacommander -- "C:\TiaCommander\TiaCommander.exe"
```

This registers TiaCommander globally. No config file editing needed. No restart required — Claude Code picks up new MCP servers immediately.

Verify with `/mcp` to see TiaCommander listed:

![Claude Code mcp add command](https://tiacommander.com/img/github/1-Quickstart/quickstart_claude-code_ss1.png)

![TiaCommander tools loaded in Claude Code](https://tiacommander.com/img/github/1-Quickstart/quickstart_claude-code_ss2.png)

</details>

<details>
<summary><strong>VS Code + GitHub Copilot</strong></summary>

**Global:** Press `Ctrl+Shift+P` → type **MCP: Open User Configuration** → this opens your global MCP config file. Add the TiaCommander server entry:

```json
{
  "servers": {
    "tiacommander": {
      "type": "stdio",
      "command": "C:\\TiaCommander\\TiaCommander.exe",
      "args": []
    }
  }
}
```

**Per-project:** Create `.vscode/mcp.json` in your project root (same format as above).

You can also configure via the Command Palette: press `Ctrl+Shift+P` → type **MCP: Add Server** → select **Command (stdio)** → enter `C:\TiaCommander\TiaCommander.exe` as the command → press Enter for empty args → type `tiacommander` as the server ID → select **User** for global scope.

**Starting the server:** If TiaCommander is not running, press `Ctrl+Shift+P` → type **MCP: List Servers** → select **tiacommander** → select **Start**.

VS Code + GitHub Copilot — verify tools are loaded:

![VS Code MCP tools loaded](https://tiacommander.com/img/github/1-Quickstart/quickstart_vscode_ss1.png)

![VS Code Agent Mode with TiaCommander](https://tiacommander.com/img/github/1-Quickstart/quickstart_vscode_ss2.png)

**Important:** GitHub Copilot only uses MCP tools in **Agent Mode**. Select **Agent** from the mode dropdown in the chat input area. To ensure Copilot calls TiaCommander instead of answering from general knowledge, prefix your prompt with **MCP** — for example: "MCP connect to TIA Portal and list all devices".

</details>

<details>
<summary><strong>Visual Studio 2022/2026 + GitHub Copilot</strong></summary>

**Global (all solutions):** Create or edit `%USERPROFILE%\.mcp.json` (typically `C:\Users\<username>\.mcp.json`):

```json
{
  "servers": {
    "tiacommander": {
      "type": "stdio",
      "command": "C:\\TiaCommander\\TiaCommander.exe",
      "args": []
    }
  }
}
```

**Per-solution:** Create `.mcp.json` in your solution root (same format as above).

You can also configure via **Copilot Chat → Tools → Add MCP Server**.

Visual Studio + GitHub Copilot — verify tools are loaded:

![Visual Studio MCP tools loaded](https://tiacommander.com/img/github/1-Quickstart/quickstart_visual-studio_ss1.png)

![Visual Studio Agent Mode with TiaCommander](https://tiacommander.com/img/github/1-Quickstart/quickstart_visual-studio_ss2.png)

**Important:** GitHub Copilot only uses MCP tools in **Agent Mode**. Select **Agent** from the mode dropdown at the bottom of the Copilot Chat panel. To ensure Copilot calls TiaCommander instead of answering from general knowledge, prefix your prompt with **MCP** — for example: "MCP connect to TIA Portal and list all devices".

</details>

<details>
<summary><strong>Cursor</strong></summary>

**Option 1 — GUI:** Open **Cursor Settings** (`Ctrl+Shift+P` → "Cursor Settings") → **Tools & MCP** → click **New MCP Server** → select **Command (stdio)** → enter `C:\TiaCommander\TiaCommander.exe` as the command → type `tiacommander` as the server name → click **Save**.

**Option 2 — Global config file:** Edit `%USERPROFILE%\.cursor\mcp.json` (typically `C:\Users\<username>\.cursor\mcp.json`):

```json
{
  "mcpServers": {
    "tiacommander": {
      "command": "C:\\TiaCommander\\TiaCommander.exe",
      "args": []
    }
  }
}
```

**Per-project:** Create `.cursor/mcp.json` in your project root (same format as above).

**Verify:** Open **Cursor Settings** → **Tools & MCP** — TiaCommander should show a green status indicator.

**Use:** Switch to **Agent** mode in the chat panel (`Ctrl+.`). If necessary, prefix prompts with **MCP** to ensure Cursor calls TiaCommander tools.

Cursor — verify TiaCommander is loaded and working:

![Cursor MCP settings with TiaCommander](https://tiacommander.com/img/github/1-Quickstart/quickstart_cursor_ss1.png)

![Cursor MCP server running](https://tiacommander.com/img/github/1-Quickstart/quickstart_cursor_ss2.png)

![Cursor Agent Mode with TiaCommander tools](https://tiacommander.com/img/github/1-Quickstart/quickstart_cursor_ss3.png)

</details>

<details>
<summary><strong>Windsurf</strong></summary>

**Option 1 — GUI:** In the Cascade panel (`Ctrl+L`), click the **MCPs icon** (top-right) → click **Add** or browse the MCP Marketplace → search for your server or add manually.

**Option 2 — Settings:** Press `Ctrl+Shift+P` → type **Open Windsurf Settings** → navigate to **Cascade** → **MCP Servers** → add the server entry.

**Option 3 — Config file:** Edit `%USERPROFILE%\.codeium\windsurf\mcp_config.json` (typically `C:\Users\<username>\.codeium\windsurf\mcp_config.json`):

```json
{
  "mcpServers": {
    "tiacommander": {
      "command": "C:\\TiaCommander\\TiaCommander.exe",
      "args": []
    }
  }
}
```

**Verify:** Click the **MCPs icon** in the Cascade panel — TiaCommander should show a green status indicator. If it doesn't appear, click **Refresh** to reload the server list.

Windsurf — verify TiaCommander is loaded and working:

![Windsurf MCP settings with TiaCommander](https://tiacommander.com/img/github/1-Quickstart/quickstart_windsurf_ss1.png)

![Windsurf MCP server running](https://tiacommander.com/img/github/1-Quickstart/quickstart_windsurf_ss2.png)

![Windsurf Cascade with TiaCommander tools](https://tiacommander.com/img/github/1-Quickstart/quickstart_windsurf_ss3.png)

</details>

<details>
<summary><strong>Codex CLI (OpenAI)</strong></summary>

Run this command:

```
codex mcp add tiacommander -- "C:\TiaCommander\TiaCommander.exe"
```

Or add manually to `%USERPROFILE%\.codex\config.toml`:

```toml
[mcp_servers.tiacommander]
command = "C:\\TiaCommander\\TiaCommander.exe"
args = []
```
Codex CLI — verify TiaCommander is loaded:

![Codex CLI with TiaCommander](https://tiacommander.com/img/github/1-Quickstart/quickstart_codex-cli_ss1.png)

</details>

<details>
<summary><strong>Gemini CLI (Google)</strong></summary>

**Config file:** `%USERPROFILE%\.gemini\settings.json` (global) or `<project>/.gemini/settings.json` (per-project)

```json
{
  "mcpServers": {
    "tiacommander": {
      "command": "C:\\TiaCommander\\TiaCommander.exe",
      "args": []
    }
  }
}
```
Gemini CLI — verify TiaCommander is loaded:

![Gemini CLI with TiaCommander](https://tiacommander.com/img/github/1-Quickstart/quickstart_gemini-cli_ss1.png)

![Gemini CLI TiaCommander tools](https://tiacommander.com/img/github/1-Quickstart/quickstart_gemini-cli_ss2.png)

</details>

<details>
<summary><strong>Other MCP clients</strong></summary>

Any MCP-compatible client that supports stdio transport can use TiaCommander. The generic configuration:

- **Transport:** stdio
- **Command:** `C:\TiaCommander\TiaCommander.exe`
- **Arguments:** none

Consult your client's MCP documentation for the exact config format.

If you find that your favorite AI assistant does not work with TiaCommander, please [file an issue](https://github.com/a4webdev/tiacommander-mcp/issues) and we will test it and fix it for you.

</details>

## 5. Restart your AI client

Close and reopen your AI client (e.g. quit Claude Desktop from the tray icon, then relaunch).
TiaCommander starts automatically when the AI client connects.

**Note:** Some clients like Claude Code don't require a restart — they pick up MCP servers immediately.

**If quitting from the tray doesn't work:** open Task Manager (Ctrl+Alt+Delete → Task Manager), find your AI client process (e.g. Claude, Cursor, Code), and click End Task. Then relaunch the application.

## 6. Activate your license

Ask your AI assistant:

- "Open the TiaCommander manager" or "open manager"

The Manager window will open. If it doesn't appear on screen, check the taskbar system tray (near the clock) — the TiaCommander icon may be there. Click it to bring the window to the foreground. This behavior varies between Windows versions; normally it should appear directly.

Register with your name, surname, and email to get a free beta license key. Activation will take ~30s. You should see the log in the terminal. Once activated, all tools become available. If automatic activation does not work, check your email — you should have received a license key. Paste it in and hit Activate.

![TiaCommander Manager registration](https://tiacommander.com/img/github/1-Quickstart/quickstart_manager_registration_ss22.png)

![TiaCommander Manager activation](https://tiacommander.com/img/github/1-Quickstart/quickstart_manager_activations_ss23.png)

#### Important: restart after activation (one-time setup)

Before activation, TiaCommander runs with only 2 tools (`get_info` and `open_manager`). After activating your license, you must restart your AI client so it reconnects and loads all 16 tools:

1. In the TiaCommander terminal window, type `stop` and press Enter to shut down the MCP server process
2. Open Task Manager (Ctrl+Alt+Delete → Task Manager)
3. Find your AI client process (e.g. Claude, Cursor, Code) and click **End Task**
4. Relaunch your AI client

After restart, your AI assistant will have access to all 16 tools. This is a **one-time setup** — subsequent launches will load all tools automatically.

Then configure the default project and archives root for your TIA Portal projects. This allows you to keep everything organised as you are used to and use TiaCommander in the same directory. You can always explicitly set a different path in the MCP Tab or just tell your AI assistant to create a project or archive in a different root.

![TiaCommander Manager MCP configuration tab](https://tiacommander.com/img/github/1-Quickstart/quickstart_manager_mcpTab_ss24.png)

**Note:** Some AI clients may need to load MCP tools before you can use them. Common commands:

- `/mcp` — load MCP tools (Claude Code, some CLI clients)
- `load tools` — trigger tool discovery
- Check your AI client's documentation for MCP tool activation

## 7. Start working

Ask your AI assistant to launch TIA Portal or connect to an already running instance:

- "Launch TIA Portal" or "Connect to TIA Portal"
- "Create a new project called MyProject"
- "Open project at C:\TiaProjects\MyProject"
- "List all devices in the project"

**Blocks:**
- "Show me all blocks in the PLC"
- "Create an FB for motor control with start/stop inputs"
- "Export all blocks as XML"
- "Find all unused blocks in the project"
- "Compile all blocks and show errors"

**Tag tables:**
- "List all tag tables"
- "Show me all tags in the Default tag table"
- "Create a tag table called Motors"
- "Add a Bool tag MotorRunning at %M100.0"

**Watch tables:**
- "List all watch tables"
- "Create a watch table for debugging motor signals"
- "Add MotorRunning tag to the watch table"

**Cross references:**
- "Find all references to MotorRunning"
- "Find all unused tags in the project"
- "Show callers of FB_MotorControl"

**Library:**
- "List all blocks in the project library"
- "Copy FB_MotorControl to the project library"
- "List all types in the global library"

**Hardware & diagnostics:**
- "Export the full I/O address map"
- "Show the hardware configuration"
- "Compare online and offline versions"
- "Scan for devices on the network"
- "Download the project to the PLC"

**Project management:**
- "Save the project"
- "Archive the project to C:\TiaArchives"
- "Get project info"


**Note:** The first time TiaCommander connects to TIA Portal, Windows will display an Openness security dialog asking you to allow external access. Click **Yes to all** — this is a one-time confirmation.

![TIA Portal Openness security dialog](https://tiacommander.com/img/github/1-Quickstart/quickstart_tiaportal_ss25.png)

Don't forget to tell us how it goes!

## Troubleshooting

**"Access denied" or connection refused:**
→ Check the Openness group membership (step 2). You must restart your PC after adding.

**AI client doesn't show TiaCommander tools:**
→ Verify the path in your config file uses double backslashes (`\\`).
→ Restart the AI client completely (not just close the window — quit from tray). If quitting from the tray doesn't work, use Task Manager (Ctrl+Alt+Delete) to end the process, then relaunch.

**TIA Portal not detected:**
→ Make sure TIA Portal is installed on your machine. TiaCommander loads Siemens Openness DLLs from your TIA Portal installation at runtime — without TIA Portal, TiaCommander cannot start.
→ Make sure TIA Portal is running with a project open before connecting.
→ Try: "Connect to TIA Portal" in your AI assistant.

## Need help?

- Check [Known Limitations](KNOWN_LIMITATIONS.md)
- Visit [tiacommander.com](https://tiacommander.com)
- File an issue on [GitHub](https://github.com/a4webdev/tiacommander-mcp/issues)

---

## Trademarks

TiaCommander™ is a trademark of SIA A4 Studio. Siemens, TIA Portal, SIMATIC, S7-1200, S7-1500 are trademarks of Siemens AG. TiaCommander is not affiliated with, endorsed by, or sponsored by Siemens AG.
