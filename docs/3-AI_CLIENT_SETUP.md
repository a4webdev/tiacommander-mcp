# AI Client Setup

TiaCommander works with any AI client that supports the Model Context Protocol (MCP) over stdio. Below are configuration instructions for all supported clients.

Sample config files are in the `configs/` folder — copy the one for your client and adjust the path if needed.

The default install path is `C:\TiaCommander\TiaCommander.exe`. If you extracted to a different location, update the path accordingly. Always use double backslashes (`\\`) in JSON paths.

---

## Claude Desktop

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

After saving, quit Claude Desktop from the tray icon and relaunch. If quitting from the tray doesn't work, use Task Manager (Ctrl+Alt+Delete) to end the process, then relaunch.

---

## Claude Code (CLI)

Run this command in your terminal:

```
claude mcp add --transport stdio --scope user tiacommander -- "C:\TiaCommander\TiaCommander.exe"
```

This registers TiaCommander globally. No config file editing needed.

---

## VS Code / Visual Studio + GitHub Copilot

**Config file:** `<project>/.vscode/mcp.json` (VS Code) or `<solution>/.mcp.json` (Visual Studio)

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

You can also configure via the Command Palette: **MCP: Add Server**.

---

## Cursor

**Config file:** `%USERPROFILE%\.cursor\mcp.json` (global) or `<project>/.cursor/mcp.json` (per-project)

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

---

## Windsurf

**Config file:** `%USERPROFILE%\.codeium\windsurf\mcp_config.json`

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

---

## Codex CLI (OpenAI)

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

---

## Gemini CLI (Google)

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

---

## Other MCP clients

Any MCP-compatible client that supports stdio transport can use TiaCommander. The generic configuration:

- **Transport:** stdio
- **Command:** `C:\TiaCommander\TiaCommander.exe`
- **Arguments:** none

Consult your client's MCP documentation for the exact config format.

If you find that your favorite AI assistant does not work with TiaCommander, please [file an issue](https://github.com/a4webdev/tiacommander-mcp/issues) and we will test it and fix it for you.
