# Changelog

All notable changes to TiaCommander are documented here.

## v2.26.0 (26-05-2026)
- **Breaking:** Siemens Openness DLLs (`Siemens.Engineering.dll`, `Siemens.Engineering.Hmi.dll`) are no longer included in the download. TiaCommander now loads them directly from your TIA Portal installation at runtime.
- **Requirement:** TIA Portal V15.1 or later must be installed on your machine. TiaCommander will not start without it.
- New: runtime assembly resolver automatically discovers your TIA Portal installation via Windows registry, with filesystem fallback
- New: startup log shows which TIA Portal installation was found and how it was discovered
- Improved: release security gate now checks for accidental inclusion of proprietary DLLs
- Retracted download assets from v2.14.1, v2.25.0, v2.25.5 — these releases contained proprietary Siemens DLLs that should not have been redistributed

## v2.25.5 (22-05-2026)
- New: User Guide (`docs/USER_GUIDE.md`) -- covers Manager commands, auto-update workflow, and general usage
- Updated CHANGELOG for v2.25.1-v2.25.4

## v2.25.4 (22-05-2026)
- Fixed: self-update no longer aborts when the AI assistant keeps a headless MCP server process running — the updater now force-kills remaining processes after a 10-second grace period instead of aborting
- Improved: update confirmation prompt now shows numbered steps explaining what will happen (download, close, replace, reopen, restart AI assistant)
- Self-update pipeline tested end-to-end: v2.25.1 → v2.25.4 via GitHub Releases

## v2.25.3 (22-05-2026)
- Improved: update confirmation prompt lists step-by-step actions before proceeding

## v2.25.2 (22-05-2026)
- First published GitHub Release with portable ZIP asset (self-update download target)
- Public repository (`a4webdev/tiacommander-mcp`) enabled for unauthenticated asset downloads

## v2.25.1 (22-05-2026)
- Security: telemetry data is now cryptographically bound to your license key
- Improved: update check handles server rate limiting gracefully

## v2.25.0 (17-05-2026)
- Unified export filename convention: all exports now use `{ProjectName}_{PLCName}_{ExportType}_{timestamp}.{ext}`
- Tag, watch, force, and alarm exports automatically generate descriptive filenames when no output path is specified
- Alarm individual exports (`export_alarm_texts`, `export_textlists`, `export_alarm_classes`) no longer require an explicit output path
- Project and PLC names are sanitized in filenames (spaces to underscores, special characters removed)

## v2.24.0 (17-05-2026)
- Fixed: `export_io_map` now correctly maps I/O addresses to individual hardware slots on S7-1200 (previously all addresses landed on CPU slot)
- Fixed: Digital I/O modules now show bit-level pinout (I8.0, I8.1, ...) instead of word-level addresses
- New: `export_hardware_map` action — exports raw Siemens CAx/AML hardware data as a flat table (single sheet XLSX or CSV)
- New filename convention for hardware exports: `{ProjectName}_{PLCName}_{ExportType}_{timestamp}.{ext}`
- Tag names and comments are cross-referenced for every I/O address in the pinout

## v2.23.0 (16-05-2026)
- New: `export_io_map` action on the hardware tool — exports a complete device I/O map combining rack/slot topology with tag assignments
- XLSX output includes one sheet per hardware slot plus a summary sheet with DI/DO/AI/AO/HSC totals
- CSV output provides a flat combined view with all slots in one file
- Supports single-device or all-device export (one file per device)
- Every physical address is listed — unassigned addresses show blank tag name/comment

## v2.22.0 (16-05-2026)
- New: `open_file` action on the admin tool — opens any exported file using the Windows default application (Excel for .xlsx, browser for .html, etc.)
- Improved: all export actions now prompt to open the exported file after completion

## v2.21.0 (16-05-2026)
- Fixed: stale TIA Portal handle no longer causes crashes when switching between launch/connect/create operations. Session create is now fully self-contained — auto-launches or attaches to TIA Portal and creates the project in one call.
- Fixed: existing block numbers are preserved when updating blocks with overwrite. No more unintended OB number increments.
- Improved: watch and force table entries now use data-type-appropriate display formats (Bool, DEC_signed, Float) instead of defaulting to Hex.
- Improved: project create, archive, and library operations now default to configured root paths. No need to specify directories manually if configured via Manager → Paths tab.

## v2.20.0 (16-05-2026)
- Comprehensive download configuration handling — 3-tier config handler covers all 30+ Openness download prompts (certificate trust, firmware mismatch, alarm text libraries, etc.) instead of only 7
- Added `pcInterfaceName` parameter to `download_to_device` for explicit network adapter selection on multi-NIC PCs (VPN + WiFi scenarios)
- Hardware downloads now auto-stop PLC modules (required for hardware configuration loading)
- Firmware mismatch guidance in tool description — directs users to Online & Diagnostics to check actual PLC firmware version
- Improved download error reporting with resolved interface and target address info

## v2.19.2 (16-05-2026)

- Fixed `create_textlist` failing silently on devices with zero existing text lists.
- New seed template approach: TiaCommander ships a template XLSX that bootstraps empty devices, then cleans up automatically.

## v2.19.1 (16-05-2026)

- New **Paths** tab in Manager GUI: consolidates all folder configuration in one place (previously on MCP Config tab).
- New `libraries_root` setting: configure a default folder for global libraries (.al19) and library archives (.zal19).
- `open_global_library` without a path now lists available libraries and archives from the configured libraries root.
- `create_global_library` and `archive_global_library` default their `targetDirectory` to the libraries root when not specified.
- `session configure` now accepts `librariesRoot` parameter alongside existing `projectsRoot`, `archivesRoot`, `exportsRoot`.
- Explicit path parameters always override the configured default.

## v2.19.0 (16-05-2026)

- Auto-compile after block creation/import: all create and import actions now automatically compile the block and return compile errors inline. No extra tool call needed to check for errors.
- New optional `autoCompile` parameter (default true). Set to false for bulk imports where you want to call compile_all at the end instead.

## v2.18.2 (16-05-2026)

- Block number auto-assignment: fbNumber, fcNumber, obNumber, blockNumber parameters now clearly documented as optional with auto-increment. AI agents no longer need to specify block numbers — the server assigns the next sequential number automatically.

## v2.18.1 (16-05-2026)

- Added LAD instruction catalog to blocks_write tool description: all 50 instruction types with pin names, data types, and usage notes
- Fixed NORMALIZE instruction: default DestType changed from Int to Real (was causing import errors with Real inputs)
- Fixed DECO/ENCO instructions: added required DisabledENO attribute and correct version numbers (V4.5+ firmware only)
- Verified JUMP, JUMPNOT, SWITCH, JMPLIST instructions with cross-network label wiring on S7-1200 V3.0

## v2.17.3 (15-05-2026)

- Fixed LAD block import on S7-1200 V3.0 firmware: removed DisabledENO attribute from 13 instruction types (MOVE, AND, OR, XOR, INV, SHL, SHR, RL, RR, DECO, ENCO, SEL, CONVERT, ROUND, CEIL, FLOOR, TRUNC, SCALE_X, NORMALIZE, MOD, NEG, ABS, EXPT, CALC)
- Fixed R_TRIG/F_TRIG chaining: eno output pin now properly connects to the next element's en input
- Fixed FB/FC Call rung parsing: now accepts both flat format (`{type:"call", blockName:...}`) and nested format (`{call:{blockName:...}}`)
- Improved tool descriptions: documented instance DB requirements for R_TRIG, F_TRIG, TON, TOF, TP, CTU, CTD, CTUD; documented LAD rung formats in networks parameter

## v2.17.2 (15-05-2026)
- Hardware tool: added guidance for new-project PLC setup workflow (network scan → identify by MAC address → assign IPs → configure connection → download)
- Hardware tool: added V4.5+ firmware note — directs user to set protection level when compile fails with "Password must not be empty"

## v2.17.1 (15-05-2026)
- `save_as` no longer requires an explicit directory — defaults to the configured projects root when `newParentDirectory` is omitted (consistent with `create` and `archive`)

## v2.17.0 (14-05-2026)
- Auto-update mechanism: type `update` in Manager terminal to check for new versions, view release notes, and install with one command
- Manager checks for updates on startup and shows a notification if a newer version is available
- Headless MCP server shows update notification in terminal when a newer version is detected via license validation
- Updates are downloaded directly from GitHub Releases, backed up before applying, and automatically restored if anything goes wrong

## v2.16.3 (13-05-2026)
- Terminal now logs confirmation when projects or archives root is changed via the Browse button in Manager GUI

## v2.16.2 (13-05-2026)
- `get_info` now shows project count next to projects root path, matching the archives count format

## v2.16.1 (13-05-2026)
- New action: `session action=list_archives` — list all `.zap*` archive files from configured or explicit archives root
- Shows filename, size (MB), last modified date, and TIA Portal version per archive
- Archives sorted newest-first; scans 1 level deep with 2-level fallback
- `get_info` now shows archive count when archives root is configured
- Optional `archivesRoot` parameter to scan a custom directory

## v2.16.0 (13-05-2026)
- Configurable default folders: set a TIA Portal Projects Folder and Archives Folder so you don't need to type full paths every time
- New GUI folder pickers on the MCP Configuration tab in TiaCommander Manager
- `session action=configure` — set `projectsRoot` and `archivesRoot` via MCP (validates directory exists)
- `session action=create` — `parentDirectory` is now optional when a projects root is configured
- `session action=open` — omit `projectPath` to list all available projects from the configured root (scans 2 levels deep)
- `session action=archive` — `outputDirectory` is now optional when an archives root is configured
- `get_info` now shows configured projects and archives root paths
- Fixed: Show Terminal checkbox state now persists correctly across restarts

## v2.15.0 (12-05-2026)
- New action: `hardware → set_network_config` — configure IP address, subnet mask, default gateway, PROFINET device name, IP protocol selection, ISO protocol, and station name on project devices
- PROFINET device name can be set directly (auto-disables auto-generation)
- Station name (Device.Name) can be renamed, which controls PROFINET name when auto-generation is on
- All changes require confirmation and download to PLC to take effect
- Use `mode=hardware` for download when changing PROFINET device name
- Known limitations documented for Openness V19 API constraints

## v2.14.1 (10-05-2026)
- Fixed user identity display in community form
- TIA Portal version dropdown expanded to all Openness-supported versions (V14 SP1 through V21 plus Other)
- Links in README help page now open in external browser
- Improved submission experience for licensed users
- Community page cosmetic fixes

## v2.14.0 (10-05-2026)
- Community form: licensed users are automatically identified when using the GitHub tab in Manager
- Removed telemetry timer message from terminal output

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
- Binary protection with multi-layer obfuscation
- Enhanced local data security

## v2.8.4 (04-05-2026)
- Resend License Key button for registration recovery

## v2.8.3 (04-05-2026)
- Internal naming cleanup and consistency fixes

## v2.8.2 (04-05-2026)
- Renamed from SiemensMcpServer to TiaCommander
- Updated all branding, assembly info, and deployment paths

## v2.8.1 (29-04-2026)
- Telemetry reporting fixes

## v2.8.0 (28-04-2026)
- Telemetry reporting for product improvement
- Data retention management

## v2.7.3 (27-04-2026)
- MCP Config tab: server entry snippet with copy button for supported AI clients

## v2.7.2 (27-04-2026)
- License validation improvements with clear error messages
- Beta auto-renewal (30-day rolling)
- Offline grace period for intermittent connectivity

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
- Usage analytics for product improvement
- `admin` meta-tool: call statistics, device profiles, version info, system info

## v2.2.0 (22-04-2026)
- `alarm_text` meta-tool with 14 actions
- Text list CRUD (create, get entries, add/update/delete entries)
- XLSX export/import for alarm texts, text lists, and alarm classes

## v2.1.0 (22-04-2026)
- Bulk operations for tags, watch table entries, and networks
- UDT member update (type, initial value, comment)
- DB member management (add, delete, update with access flags)

## v2.0.0 (21-04-2026)
- Meta-tool architecture: consolidated into 9 domain-grouped meta-tools
- Improved scalability and AI client compatibility

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
