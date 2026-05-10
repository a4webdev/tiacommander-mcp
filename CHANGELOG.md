# Changelog

All notable changes to TiaCommander are documented here.

## v2.13.3 (10-05-2026)
- Fixed terminal input bar right-edge alignment with scrollbar
- Fixed prompt chevron vertical alignment with cursor

## v2.13.2 (10-05-2026)
- Improved first-time telemetry reporting accuracy

## v2.13.1 (10-05-2026)
- AI agent session tracking in telemetry

## v2.13.0 (08-05-2026)
- MCP protocol compliance improvements
- Tool annotations (readOnlyHint for read-only tools, destructiveHint for PLC download)
- Human-readable tool titles for all 16 tools
- Server metadata enrichment (title, description, website, usage instructions)
- Cancellation notification handler

## v2.12.0 (08-05-2026)
- MCP Configuration tab with support for 8 AI clients (Claude Desktop, Claude Code, VS Code/Copilot, Cursor, Windsurf, Codex CLI, Gemini CLI)
- Terminal command input improvements

## v2.10.0 (07-05-2026)
- Consolidated tool surface to 16 tools
- Default to GUI mode (withUI=true) for TIA Portal
- Openness group membership guard on startup
- Download PG/PC interface prerequisite check
- MCP ping support
- Crash-safe error responses

## v2.9.1 (06-05-2026)
- Terminal output sanitized — no sensitive data displayed
- Manager window always visible on launch

## v2.9.0 (06-05-2026)
- Binary protection: 11-layer obfuscation with NecroBit virtualization
- Local database encryption: AES-256-CBC on sensitive license fields

## v2.8.4 (04-05-2026)
- Resend License Key button for registration recovery

## v2.8.3 (04-05-2026)
- Internal naming cleanup and consistency fixes

## v2.8.2 (04-05-2026)
- Renamed from SiemensMcpServer to TiaCommander
- Updated all branding, assembly info, and deployment paths

## v2.8.1 (29-04-2026)
- Telemetry validation fixes (JSON key alignment, aggregation query fixes)

## v2.8.0 (28-04-2026)
- Telemetry reporting with server-controlled intervals
- Automatic telemetry on startup
- Data retention management (90-day purge)

## v2.7.3 (27-04-2026)
- MCP Config tab: server entry snippet with copy button for supported AI clients

## v2.7.2 (27-04-2026)
- License API improvements: failure reason codes, account status checks
- Beta auto-renewal (30-day rolling)
- Startup auto-validation with offline grace period fallback

## v2.7.0 (25-04-2026)
- TiaCommander Manager GUI with Help and GitHub tabs
- License gating: two-process architecture (headless MCP server + Manager GUI)
- License panel with registration, activation, re-validate, and deactivate

## v2.6.0 (24-04-2026)
- License system: tool access gating based on license status
- Manager GUI launch from MCP tool call
- On-startup license detection

## v2.5.0 (23-04-2026)
- Assignment list: I/O address occupancy for M/I/Q areas
- Hardware I/O integration
- Cross-reference conflict detection
- `find_next_free` with alignment support (Bool/Byte/Word/DWord)

## v2.4.0 (22-04-2026)
- Standalone I/O: all imports accept inline XML content
- All exports support inline return mode
- Export database with retrieval, paging, and round-trip reimport
- Zero dependency on external file system tools

## v2.3.0 (22-04-2026)
- Local product database (SQLite) for usage analytics
- `admin` meta-tool: call statistics, device profiles, error log, version info, system info

## v2.2.0 (22-04-2026)
- `alarm_text` meta-tool with 14 actions
- Text list CRUD (create, get entries, add/update/delete entries)
- XLSX export/import for alarm texts, text lists, and alarm classes

## v2.1.0 (22-04-2026)
- Bulk operations for tags, watch table entries, and networks
- UDT member update (type, initial value, comment)
- DB member management (add, delete, update with access flags)

## v2.0.0 (21-04-2026)
- Meta-tool architecture: 47 flat tools consolidated into 9 domain-grouped meta-tools
- Action discriminator pattern for scalable tool surface

## v1.13 (18-04-2026)
- LAD/FBD block creation with 69 verified instruction types
- Auto-increment block numbers

## v1.12 (16-04-2026)
- `download_upload` meta-tool (PLC deployment with pre-flight checks, upload station)
- Confirmation strings required for destructive PLC operations

## v1.11 (14-04-2026)
- `xref` cross-reference analysis (callers, unused blocks, orphaned instance DBs)

## v1.10 (12-04-2026)
- Library harmonization: update_project, promote_to_global, compare_to_target, delete_unused_types

## v1.9 (10-04-2026)
- Global library support: open, close, create, save, archive .al19 files
- Library type instantiation with version selection

## v1.8 (08-04-2026)
- Master copy operations: publish blocks and UDTs, instantiate, delete
- Library folder management

## v1.7 (05-04-2026)
- `library` meta-tool: browse project and global libraries, search, inspect types and versions

## v1.6 (03-04-2026)
- Library infrastructure: list libraries, get info, folder navigation, tree view

## v1.5 (01-04-2026)
- Session lifecycle: create, save_as, archive projects
- Project-level dirty check and save operations

## v1.4 (28-03-2026)
- Meta-tool consolidation phase 2: remaining flat tools merged into domain groups

## v1.3 (25-03-2026)
- Meta-tool architecture: began consolidating 47 flat tools into domain-grouped meta-tools with action discriminators

## v1.2 (18-03-2026)
- UDT operations: create, delete, add/remove members, XML import/export

## v1.1 (10-03-2026)
- Diagnostics: PLC online/offline state, network scan
- Compiler integration: compile blocks, compile all, error retrieval

## v1.0 (28-02-2026)
- Hardware configuration and network topology
- Device details, rack/slot layout, I/O address map
- CSV and XLSX export for hardware data

## v0.9 (15-02-2026)
- Watch and force table management (11 actions)
- XML import/export for watch tables

## v0.8 (01-02-2026)
- Block write operations: create FB/FC/OB in SCL/LAD/FBD/STL
- Block deletion and XML import

## v0.7 (20-01-2026)
- Tag table operations: create tables, add/delete tags, search by name
- Address assignment and tag comments

## v0.6 (08-01-2026)
- Data block operations: global DB and instance DB creation
- DB member management (add, delete, update)

## v0.5 (22-12-2025)
- Data block read operations: list, inspect structure via XML parse

## v0.4 (08-12-2025)
- Block export: XML and SCL/STL source export
- Multi-block export to directory

## v0.3 (24-11-2025)
- Block read operations: list blocks with type/language, inspect details and interfaces

## v0.2 (10-11-2025)
- Session management: connect, disconnect, open/close projects, list devices
- TIA Portal V15.1–V19 compatibility

## v0.1 (25-10-2025)
- Initial release: MCP server architecture (JSON-RPC 2.0 over stdio)
- TIA Portal Openness API integration
