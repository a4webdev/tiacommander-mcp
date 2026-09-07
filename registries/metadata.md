# TiaCommander — Registry Metadata

**Name:** TiaCommander
**One-liner:** MCP server that connects AI assistants to Siemens TIA Portal via the Openness API
**Description:** AI-assisted PLC programming, project management, library operations, hardware configuration, cross-reference analysis, and deployment for Siemens TIA Portal. 19 tools, 230 actions covering the full TIA Portal project lifecycle. Tested with Claude Desktop, Claude Code, VS Code (Copilot), Cursor, Windsurf, Codex CLI, Gemini CLI.
**Category:** industrial-automation
**Tags:** siemens, tia-portal, plc, industrial-automation, openness-api, scl, lad
**Transport:** stdio
**Runtime:** .NET Framework 4.8 (Windows)
**License:** Proprietary (free beta)
**Homepage:** https://tiacommander.com
**Repository:** https://github.com/a4webdev/tiacommander-mcp
**Author:** SIA A4 Studio
**Icon (primary):** https://tiacommander.com/img/favicon512_padded.png
**Icon (GitHub):** https://raw.githubusercontent.com/a4webdev/tiacommander-mcp/master/registries/icon.png

## Install snippet

```json
{
  "mcpServers": {
    "tiacommander": {
      "command": "C:\\TiaCommander\\TiaCommander.exe"
    }
  }
}
```

## awesome-mcp-servers line

```
- [TiaCommander](https://github.com/a4webdev/tiacommander-mcp) - MCP server for Siemens TIA Portal — AI-assisted PLC programming, hardware config, cross-references, deployment via the Openness API.
```