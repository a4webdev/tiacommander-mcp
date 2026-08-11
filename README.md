# TiaCommander — MCP Server for Siemens TIA Portal

| | |
|---|---|
| **Version** | 3.15.2 |
| **Protocol** | MCP 2024-11-05 (stdio) |
| **Runtime** | .NET Framework 4.8 |
| **TIA Portal** | V17 – V20 (Openness API, tested on V19) |
| **License** | Proprietary (free beta) |

**New in this release:** live PLC data — read data blocks, tag tables, watch tables and CPU diagnostics live from the running controller; several AI clients served at once from one machine, with optional direct HTTP clients on the local listener; and multi-Portal support — pick which installed TIA Portal (V17–V20) to run on a machine with several installed.

*\*V17 through V20 are supported for project work. V16 and older are detected and refused with a clear message. Only V19 is fully tested; the others are expected to work as TIA Portal maintains backward-compatible Openness APIs across versions.*


---

## Overview

TiaCommander is an MCP server that connects AI assistants to Siemens TIA Portal via the Openness API. It enables AI-assisted PLC programming, project management, library operations, hardware configuration, cross-reference analysis, and deployment — all through natural language.

**Tested AI clients:** Claude Desktop, Claude Code, VS Code (Copilot), Cursor, Windsurf, Codex CLI, Gemini CLI.

**18 tools, 205 actions** covering the full TIA Portal project lifecycle.

---

## What's in the download?

```
TiaCommander-v3.15.2-portable/
├── bridge/                              ← Live PLC data helper (S7CommPlus / TLS)
│   ├── libcrypto-3-x64.dll
│   ├── libssl-3-x64.dll
│   ├── S7CommPlusDriver.dll
│   ├── TiaCommander.S7Bridge.exe
│   ├── TiaCommander.S7Classic.dll
│   └── zlib.net.dll
├── data/
│   └── exports/                         ← Export output folders
│       ├── alarm_text/
│       ├── hardware/
│       ├── tag/
│       └── watch/
├── docs/
│   ├── configs/
│   │   ├── claude_code.txt
│   │   ├── claude_desktop_config.json
│   │   ├── codex_cli_config.toml
│   │   ├── cursor_mcp.json
│   │   ├── gemini_cli_settings.json
│   │   ├── generic_stdio.json
│   │   ├── vscode_copilot_mcp.json
│   │   └── windsurf_mcp_config.json
│   ├── 1-QUICKSTART.md
│   ├── KNOWN_LIMITATIONS.md
│   ├── LICENSE.txt
│   ├── README.md
│   └── THIRD-PARTY.md
├── help/
│   ├── github-offline.html
│   └── readme-offline.html
├── licenses/                            ← Full third-party licence texts
│   ├── Apache-2.0.txt
│   ├── BSD-3-Clause.txt
│   ├── GPL-3.0.txt
│   ├── LGPL-3.0.txt
│   ├── Microsoft-WebView2-SDK.txt
│   ├── MIT.txt
│   └── Polyform-Noncommercial-1.0.0.txt
├── Resources/
│   ├── logoBlack.png
│   └── tiacommander.png
├── runtimes/                            ← WebView2 native loaders
│   ├── win-x64/native/WebView2Loader.dll
│   ├── win-x86/native/WebView2Loader.dll
│   └── win-arm64/native/WebView2Loader.dll
├── Templates/                           ← Block XML, alarm & diagnostics templates
│   ├── diagnostics/
│   │   ├── TC_Diag_1200.xml
│   │   └── TC_DiagOB.xml
│   ├── Fb.xml
│   ├── Fc.xml
│   ├── GlobalDb.xml
│   ├── Ob.xml
│   ├── Ob_FBD.xml
│   ├── Ob_LAD.xml
│   ├── Ob_SCL.xml
│   └── TextListTemplate.xlsx
├── x64/                                 ← SQLite native library (64-bit)
│   └── SQLite.Interop.dll
├── x86/                                 ← SQLite native library (32-bit)
│   └── SQLite.Interop.dll
├── CHANGELOG.md
├── EPPlus.dll
├── EPPlus.Interfaces.dll
├── icon.png
├── manifest.json
├── Microsoft.Bcl.AsyncInterfaces.dll
├── Microsoft.IO.RecyclableMemoryStream.dll
├── Microsoft.Web.WebView2.Core.dll
├── Microsoft.Web.WebView2.WinForms.dll
├── System.Buffers.dll
├── System.ComponentModel.Annotations.dll
├── System.Data.SQLite.dll
├── System.IO.Pipelines.dll
├── System.Memory.dll
├── System.Numerics.Vectors.dll
├── System.Runtime.CompilerServices.Unsafe.dll
├── System.Security.Cryptography.Xml.dll
├── System.Text.Encodings.Web.dll
├── System.Text.Json.dll
├── System.Threading.Tasks.Extensions.dll
├── THIRD-PARTY-NOTICES.md
├── TiaCommander.exe                     ← MCP server + Manager GUI
└── TiaCommander.exe.config
```


No installer, no registry changes, no system modifications. Extract, configure, and run. Download the latest `.zip` from [GitHub Releases](https://github.com/a4webdev/tiacommander-mcp/releases).

**Claude Desktop users:** Download the `.mcpb` bundle from [Releases](https://github.com/a4webdev/tiacommander-mcp/releases/latest) and install via Settings → Extensions → Advanced Settings → Install Extension. No config file editing needed. See the [Quick Start Guide](docs/1-QUICKSTART.md) for details.

> **Prerequisite:** [Siemens TIA Portal](https://www.siemens.com/tia-portal) V15.1 or later must be installed on your machine with the **Openness** component enabled. TiaCommander loads Siemens Openness libraries directly from your TIA Portal installation at runtime. Without TIA Portal, the server starts in **degraded mode** with limited functionality and guides you through setup.

---

## Why this tool exists?

First things first — we are software developers and automation engineers who use Siemens products daily — in our own facilities, at client sites, and in every project that needs industrial automation. We love it. With Siemens, it's simple — it just works. Every time. All the time.

During years of hands-on programming, wiring, testing, debugging and making things happen with Siemens PLCs, we have built TiaCommander just for our own internal needs. That's the honest reason why it exists — we built it for ourselves, and we find it awesome. It speeds things up tenfold, sometimes twentyfold, and sometimes more — until you ask yourself: how the hell were we getting things done before?!

You can get full project context through your AI assistant in under a minute. Create any block in seconds — SCL, LAD, FBD, DB — get interface structures, build multi-instance DBs, run cross-references, compile, debug, retrieve errors, and a lot more.

Every tool you see in the tool map below is built from what we actually need or use in our daily operations with Siemens PLCs. Whether you're working with S7-1200 or S7-1500 series — it doesn't matter. If Siemens TIA Portal supports it, you can use TiaCommander to manage it.

Now that we're making this tool public — we're genuinely keen on what features you're missing for your development workflow. Don't be shy — submit a feature request, file a bug report, or just tell us what you think. As an Engineer to Engineer — we read every line of feedback.

Enjoy TiaCommander.

---

## Architecture

```
AI Clients
  Claude Desktop / Code · Cursor · VS Code / Copilot · Windsurf · Codex CLI · Gemini CLI
    │
    │  JSON-RPC 2.0 · stdio
    ▼
TiaCommander MCP Server
  18 tools · 205 actions · standalone I/O · .NET Framework 4.8
    │
    │  Siemens Openness API
    ▼
TIA Portal  (V15.1 – V21*, tested on V19)
    │
    ▼
S7-1200 · S7-1500 · S7-300 · S7-400 · ET 200
```

---

## Tool Map

| Tool / Action | # | Description |
|---|---|---|
| **`admin`** | **13** | **Server administration** |
| &nbsp;&nbsp;&nbsp;&nbsp;`clear_exports` | | (optional olderThanHours=24) Delete expired exports. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_export` | | (exportId) Delete a single export. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_device_profiles` | | All unique devices seen by the server. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_export` | | (exportId; optional offset, length, raw=false) Retrieve export content with paging. raw=true returns content only, no metadata header. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_recent_errors` | | (optional count=10) Last N failed tool calls with device/firmware context. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_system_info` | | OS, .NET version, TIA Portal version, process uptime. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_stats` | | (optional top_n=20) Call statistics per tool+action, sorted by total calls. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_version` | | Server version, DB path, DB size. |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_exports` | | (optional tool, limit=20) Recent export results stored in export DB. |
| &nbsp;&nbsp;&nbsp;&nbsp;`open_file` | | (filePath) Open a file using the Windows default application (Excel for .xlsx, browser for .html, etc.). Use after any export action to let the user inspect results immediately. |
| &nbsp;&nbsp;&nbsp;&nbsp;`reset_device_catalog` | | Clear local device catalog and re-dump from TIA Portal if connected. |
| &nbsp;&nbsp;&nbsp;&nbsp;`save_export` | | (exportId, outputPath) Save export content to a file. Auto-decodes base64 for binary formats (XLSX). After saving, ask the user if they want to open the file (use admin action=open_file). |
| &nbsp;&nbsp;&nbsp;&nbsp;`search_device_catalog` | | (optional filter, limit=50) Search installed TIA hardware catalog from local DB. No TIA Portal needed. Use for hardware reference lookups (order numbers, CPU models, compatible modules). |
| **`alarm_text`** | **15** | **Alarm texts & text lists** |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_entries` | | (deviceName, textlistName, entries[]) Add entries to a text list. Each entry: {from, to, text}. |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_textlist` | | (deviceName, textlistName; optional listRange=Decimal, comment, entries[]) Create new text list. Works on empty devices (auto-seeds via template). Optionally populate with entries in one call. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_entries` | | (deviceName, textlistName, fromValues[]) Delete entries by From value. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_textlist` | | (deviceName, textlistName) Delete a user text list. System text lists rejected. |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_alarm_classes` | | (deviceName, outputPath) Export alarm class definitions. After export, ask the user if they want to open the file (use admin action=open_file). |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_alarm_data` | | (deviceName, format:csv\|xlsx; optional scope:alarm_texts\|textlists\|alarm_classes, outputPath, includeInfoText) Export alarm data to CSV or XLSX for documentation. Defaults to exports_root\alarm_text\. After export, ask the user if they want to open the file (use admin action=open_file). |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_alarm_texts` | | (deviceName, outputPath; optional includeInfoText) Export alarm instance texts to XLSX. After export, ask the user if they want to open the file (use admin action=open_file). |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_textlists` | | (deviceName, outputPath; optional textListNames[]) Export text lists to XLSX. All if no filter. After export, ask the user if they want to open the file (use admin action=open_file). |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_entries` | | (deviceName, textlistName) Read all entries for a text list. |
| &nbsp;&nbsp;&nbsp;&nbsp;`import_alarm_classes` | | (deviceName, filePath) Import alarm class definitions. |
| &nbsp;&nbsp;&nbsp;&nbsp;`import_alarm_texts` | | (deviceName, filePath) Import alarm instance texts from XLSX. |
| &nbsp;&nbsp;&nbsp;&nbsp;`import_textlists` | | (deviceName, filePath; optional overwrite) Import text lists from XLSX. |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_textlists` | | (deviceName) List system and user text lists with name, ID, range. |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_entries` | | (deviceName, textlistName, entries[]) Update entries by From value. Each entry: {from, to?, text?}. |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_textlist_comment` | | (deviceName, textlistName, newComment) Update text list comment directly. |
| **`blocks_read`** | **16** | **Read & export blocks** |
| &nbsp;&nbsp;&nbsp;&nbsp;`check_consistency` | | (deviceName) M10: IsConsistent flag can lag compile — use compile_all output instead. |
| &nbsp;&nbsp;&nbsp;&nbsp;`compile_all` | | (deviceName) Compile all. Run early to resolve F6. Slow first run (~30-60s). |
| &nbsp;&nbsp;&nbsp;&nbsp;`compile_block` | | (deviceName, blockName) Compile one block. |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_all_xml` | | (deviceName, outputDirectory) All blocks as .xml files. M5: arg is 'outputDirectory' not 'outputPath'. After export, ask the user if they want to open the output directory (use admin action=open_file). |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_source` | | (deviceName, blockName, outputPath) SCL/STL source. M3: outputPath must be a DIRECTORY. LAD/FBD return empty. After export, ask the user if they want to open the file (use admin action=open_file). |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_xml_file` | | (deviceName, blockName, outputPath) Block XML to disk. After export, ask the user if they want to open the file (use admin action=open_file). |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_xml_inline` | | (deviceName, blockName) Parsed XML summary. F6 applies. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_all_interfaces_summary` | | (deviceName) Member counts per section for all blocks. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_compiler_errors` | | (deviceName) Error-only filter. Same M8 recompile quirk. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_compiler_messages` | | (deviceName) M8: triggers fresh compile. Prefer compile_all output if just compiled. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_details` | | (deviceName, blockName) Type, number, language, interface size, consistency. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_element_pins` | | (deviceName, blockName, networkIndex; optional elementUId or elementIndex) The 'look before you wire' card: for a Call or instruction Part, every pin with section, EXPECTED data type (read live from the callee interface / instance structure) and current wiring (operand + its resolved type, or UNWIRED OpenCon). Without a selector: lists the network's elements with UIds. Use BEFORE blocks_write u... |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_interface` | | (deviceName, blockName) Full interface sections. F6: flakes on inconsistent blocks — compile_all first. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_network` | | (deviceName, blockName, networkIndex; optional includeUIds) #171 rung editor READ side: reconstructs the network's flat Parts+Wires graph into a JSON rung model - EXACTLY the vocabulary create_block networks[].rungs accepts (contacts/parallelBranches/box/output/call, plus enFrom for ENO-cascaded call chains). Output: {title, comment, editable, rungs[], unmodelled[]}. editable=true means the mod... |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_xml_raw` | | (deviceName, blockName) Raw XML string. F6 applies. |
| &nbsp;&nbsp;&nbsp;&nbsp;`list` | | (deviceName; optional typeFilter:all\|code\|OB\|FB\|FC\|DB\|GlobalDB\|InstanceDB, includeInstanceDbs) Compact block table. |
| **`blocks_write`** | **24** | **Create & import blocks** |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_interface_member` | | (deviceName, blockName, section, memberName, dataType; optional startValue, comment, confirm) #171 interface editor: add a member to an FB/FC/OB interface section (Input, Output, InOut, Static, Temp, Constant). BLAST RADIUS is computed FIRST (xref callers whose call sites go out of date + the FB's instance DBs + the #183 reinitializeDataBlocks warning: download resets actual values of affected ... |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_multi_instance_member` | | (deviceName, parentFB, memberName, innerType; optional onCollision=fail\|replace) Add typed static member to FB. innerType must exist in device or project.ProjectLibrary. |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_network` | | (deviceName, blockName; optional position, title, comment) Insert empty network at position (default: end). LAD/FBD only — SCL blocks have a single compilation unit and do not support multiple networks. Use populate_network / insert_rung to add logic afterward. |
| &nbsp;&nbsp;&nbsp;&nbsp;`copy_block` | | (deviceName, blockName, newName; optional targetFolder, createParents=false) Duplicate a block under a new name (block names are PLC-WIDE unique, so newName is mandatory and checked recursively). targetFolder omitted = copy lands next to the source block. The copy gets the next free number of its type. Source block is never modified. OFFLINE required. |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_block` | | (deviceName, blockName, blockType:FB\|FC\|OB, language; optional blockNumber, interface, sclCode, networks, blockTitle, blockComment, author, version, memoryLayout, secondaryType, overwrite) Full block creation with custom interface and logic. blockNumber auto-assigned if omitted — ONLY for genuinely new blocks. When using overwrite=true to update an existing block (e.g. adding calls to an existi... |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_fb` | | (deviceName, fbName; optional fbNumber, language:LAD\|SCL\|FBD (STL not supported - Openness limitation), author, version, autoCompile=true, targetFolder, createParents=false) New FB. Number auto-assigned if omitted. Auto-compiles and returns errors. targetFolder ('Motors/Drives' syntax) creates the block directly inside that folder; missing folders are an ERROR unless createParents=true. |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_fc` | | (deviceName, fcName; optional fcNumber, language, returnType=Void, author, version, autoCompile=true, targetFolder, createParents=false) New FC. Number auto-assigned if omitted. Auto-compiles and returns errors. targetFolder as in create_fb. |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_ob` | | (deviceName, obName; optional obNumber, language, author, version, autoCompile=true, targetFolder, createParents=false) New OB. Number auto-assigned if omitted (starts at 123). obNumber=1 allowed; 2-122 reserved. Auto-compiles and returns errors. targetFolder as in create_fb. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_block` | | (deviceName, blockName; optional force=false) Delete any block. Instance DBs rejected unless force=true (useful for orphaned instance DBs). |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_interface_member` | | (deviceName, blockName, memberName; optional section, confirm) Remove an interface member. Same blast-radius confirm gate. Logic still referencing the member will fail compile - check first with xref get_references. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_network` | | (deviceName, blockName, networkIndex) Remove network by 0-based index. Refuses if last network. LAD/FBD only. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_rung` | | (deviceName, blockName, networkIndex, rungIndex; optional expectTitle) Remove rung rungIndex. Refuses when the NEXT rung chains (enFrom) from the deleted one - retarget or delete the tail first. |
| &nbsp;&nbsp;&nbsp;&nbsp;`import_xml_file` | | (deviceName, filePath; optional overwrite, autoCompile=true, targetFolder, createParents=false) Import from XML file. Auto-compiles and returns errors. Preferred for large payloads. targetFolder as in import_xml_inline. |
| &nbsp;&nbsp;&nbsp;&nbsp;`import_xml_inline` | | (deviceName, xmlContent; optional overwrite, autoCompile=true, targetFolder, createParents=false) Import block from XML string. Auto-compiles and returns errors. Large payloads (12KB+) verified. targetFolder imports into that folder; without it, overwrite=true auto-targets the folder the existing block already lives in (owning-group import). |
| &nbsp;&nbsp;&nbsp;&nbsp;`insert_rung` | | (deviceName, blockName, networkIndex, rung; optional position, expectTitle) #171 rung editor: insert ONE rung (create_block rung vocabulary: contacts/parallelBranches/box/output OR call{blockName,blockType,instanceDB,scope,pins}, optional enFrom for eno chaining) into an EXISTING LAD network at position (default: append). The network must be fully modelled (get_network editable=true) - refused ... |
| &nbsp;&nbsp;&nbsp;&nbsp;`move_block` | | (deviceName, blockName, targetFolder; optional createParents=false) Move a block into another folder ('' or '/' = container root). Openness V19 has NO native move — this is export -> delete -> import into the target group, with automatic restore to the original folder if the import fails. Verified by re-resolution and recompiled afterwards. OFFLINE required. |
| &nbsp;&nbsp;&nbsp;&nbsp;`populate_network` | | (deviceName, blockName, networkIndex, rungs; optional overwriteExisting, expectTitle) Fill a network with a rungs[] array (create_block networks[].rungs vocabulary incl. enFrom chains). Empty networks (fresh add_network) populate directly; non-empty networks require overwriteExisting=true AND full modelling (get_network first to preserve content). #164-style pre-import validation with JSON-path... |
| &nbsp;&nbsp;&nbsp;&nbsp;`remove_multi_instance_member` | | (deviceName, parentFB, memberName) Remove static multi-instance member. |
| &nbsp;&nbsp;&nbsp;&nbsp;`rename_block` | | (deviceName, blockName; optional newName, newNumber) Rename/renumber. Does NOT auto-cascade to callers. |
| &nbsp;&nbsp;&nbsp;&nbsp;`replace_network` | | (deviceName, blockName, networkIndex; optional title, comment) Replace network title/comment. LAD/FBD only — sclCode not supported (SCL XML is tokenized; use create_block with overwrite=true to rebuild SCL blocks). |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_interface_member` | | (deviceName, blockName, memberName; optional section, newName, newDataType, newStartValue, newComment, confirm) Update or RENAME (newName) an interface member. Section omitted = searched in all sections; miss lists members per section. Same blast-radius confirm gate. Empty-string newStartValue/newComment removes. |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_network` | | (deviceName, blockName, networkIndex; optional title, comment) Update network title/comment (0-based index). |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_network_element` | | (deviceName, blockName, networkIndex, elementUId or elementIndex; optional newInstance, newTarget, pinWires) Surgical editing of Call elements in LAD/FBD. newInstance: swap instance DB. newTarget: retarget to different FB/FC (regenerates parameters, preserves matching wires). pinWires: JSON object mapping pin names to new operands — TYPE-CHECKED before any mutation against the pin's declared ty... |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_rung` | | (deviceName, blockName, networkIndex, rungIndex, rung; optional expectTitle) Replace rung rungIndex with the given rung object. Same modelled-network requirement and renumbering caveat as insert_rung. |
| **`db`** | **11** | **Data block management** |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_member` | | (deviceName, dbName, memberName, dataType; optional comment, initialValue) Add member to global DB. Supports all types including arrays (e.g. Array[0..9] of Int) and UDT refs. Global DBs only — instance DBs rejected. |
| &nbsp;&nbsp;&nbsp;&nbsp;`create` | | (deviceName, dbName; optional dbNumber, author, version, memoryLayout, targetFolder, createParents) New global DB. Number auto-assigned if omitted. targetFolder ('Motors/Drives' syntax) creates the DB directly inside that folder; missing folders are an ERROR unless createParents=true. For instance DBs, use create_instance_db instead. |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_instance_db` | | (deviceName, dbName, instanceOfName; optional dbNumber) Create instance DB for an FB. TIA auto-populates interface from the FB. instanceOfName must be an existing FB. Number auto-assigned if omitted. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete` | | (deviceName, dbName) Delete DB. Rejects instance DBs (delete owning FB instead). |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_member` | | (deviceName, dbName, memberName) Remove member from global DB. Refuses last member. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_member` | | (deviceName, dbName, path; optional depth=2) #171 instance-data READ at ANY depth: dotted member path into nested multi-instance chains, instruction instances, UDT/struct members (e.g. 'modbus.BWTT-RR8_Instance.mb_addr'). Card shows type, start value(s) incl. per-array-element, comment, children to depth. Works on GLOBAL and INSTANCE DBs (exception: system-block iDBs cannot be exported). Member... |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_structure` | | (deviceName, dbName) Full member list with types, start values, attributes via XML export. Flat list (no nesting hierarchy). |
| &nbsp;&nbsp;&nbsp;&nbsp;`list` | | (deviceName) All global and instance DBs. Names are qualified 'Folder/Sub/Name' paths (#185). |
| &nbsp;&nbsp;&nbsp;&nbsp;`set_start_value` | | (deviceName, dbName, path, value \| values{path:value,...}) #171 instance-data WRITE at ANY depth, on global AND instance DBs (start values only, never structure - the multi-instance config surface, e.g. a station call's mb_addr default inside the poll DB). Array elements via [index] on the LAST segment ('Cfg_HwId[3]'). Values are TIA literals as strings ('33', 'TRUE', 'T#2S', '16#00FF'). ALL pa... |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_member` | | (deviceName, dbName, memberName; optional newDataType, newStartValue, newComment) Update member properties. At least one field required. Start values preserved on type change unless newStartValue explicitly provided. If TIA rejects due to type/value mismatch, provide a compatible newStartValue. Consecutive member edits can leave the DB export-inconsistent — the tool auto-compiles and retries ON... |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_member_comment` | | (deviceName, dbName, memberName, newComment) Shortcut for comment-only update. CRITICAL: arg is 'newComment', NOT 'comment'. |
| **`diagnostics`** | **7** | **PLC diagnostics & connection** |
| &nbsp;&nbsp;&nbsp;&nbsp;`compare_online_offline` | | (deviceName) Compare all software objects (blocks, tags, UDTs, technology objects) between the offline project and online PLC. Auto-connects if offline. Read-only. Returns per-item status: Identical/Different/Only on PLC/Only in project. |
| &nbsp;&nbsp;&nbsp;&nbsp;`configure_connection` | | (deviceName, pcInterfaceName, confirm; optional targetIp, skipConfirm=false) Sets up the download connection programmatically. targetIp defaults to the device's project-configured IP from the hardware config — omit it in the normal case; pass it only to target a different address (e.g. from scan_devices). Creates a ConfigurationAddress inside the TargetInterface, then applies the configuration.... |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_device_ip` | | (deviceName) Read-only. Reports the device's project-configured IP (the default target for configure_connection and download_to_device) plus every network interface with IP/mask/subnet. Call this to answer 'which IP does this PLC use' from the project — no network scan needed. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_plc_status` | | (deviceName) CONNECTION state (Online/Offline/NotReachable/Protected/...), whether the download connection is configured, AND the CPU's ACTUAL operating state, READ LIVE. RUN/STOP is absent from the engineering API entirely, so the state is obtained via live_data: this action resolves the IP of the device you NAMED, opens a live-data session if one is free, confirms the CPU's identity against t... |
| &nbsp;&nbsp;&nbsp;&nbsp;`go_offline` | | (deviceName) Disconnect from PLC. |
| &nbsp;&nbsp;&nbsp;&nbsp;`go_online` | | (deviceName) Connect to PLC via OnlineProvider. Run download_check first (silently) to verify interface is configured. |
| &nbsp;&nbsp;&nbsp;&nbsp;`scan_devices` | | (optional deviceName) Read-only network scan. Discovers accessible PLCs via GetAccessibleDevices(). Returns IP, DeviceSeries, MAC, Name per PcInterface, each ANNOTATED [IN PROJECT: <device>] or [NOT IN THIS PROJECT]. With deviceName: scans via DownloadProvider. Without: scans via StationUploadProvider (project scope). No confirmation needed. SCOPE: this is a layer-2 PROFINET DCP broadcast — it ... |
| **`download_upload`** | **4** | **Download / upload to PLC** |
| &nbsp;&nbsp;&nbsp;&nbsp;`download_check` | | (deviceName) Silent pre-flight. Call this BEFORE any download or go_online. Do not output text before calling. Verdicts: READY (all gates verified incl. compile), NOT READY (a gate FAILED — relay the issue), COMPILE UNVERIFIED (compile gate could not run, e.g. device is online — NOT a pass; download_to_device still compiles offline itself, but the check did not verify it). |
| &nbsp;&nbsp;&nbsp;&nbsp;`download_to_device` | | (deviceName, confirm; optional mode=software_changes, stopModules=false, startAfterDownload=true, reinitializeDataBlocks=false, pcInterfaceName) DESTRUCTIVE. Mandatory compile pre-check. confirm='I understand this will modify the PLC'. mode: software_changes\|software\|hardware_software\|hardware. pcInterfaceName selects which network adapter to route through (from scan_devices results). hardware/... |
| &nbsp;&nbsp;&nbsp;&nbsp;`upload_check` | | Silent pre-flight. Call this BEFORE any upload. Do not output text before calling. If result shows READY, proceed to upload_station. If NOT READY, relay the issue to the user. |
| &nbsp;&nbsp;&nbsp;&nbsp;`upload_station` | | (modeName, pcInterfaceName, confirm; optional addressIndex=0, readPassword, writePassword) DESTRUCTIVE TO PROJECT (adds device). confirm='I understand this will add a device to the project'. |
| **`folders`** | **5** | **Project-tree folders (groups)** |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_folder` | | (deviceName, container, path; optional createParents=false) Create the folder at path. Nested paths use '/' (e.g. 'Motors/Drives'). Missing intermediate folders are an ERROR unless createParents=true — never created silently. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_folder` | | (deviceName, container, path; optional deleteContents=false, confirm) DESTRUCTIVE. Empty folder deletes immediately. Non-empty requires BOTH deleteContents=true AND confirm containing 'I understand' — deletion permanently removes ALL contained items and subfolders. |
| &nbsp;&nbsp;&nbsp;&nbsp;`find` | | (deviceName, container, query; optional path, typeFilter, caseSensitive=false) Glob search across the WHOLE container: * matches any sequence, ? one character (e.g. 'mb_*', '*HMI*'). Returns Name  [key=value, ...] (same envelope and per-container keys as get_tree) + folder location per match; capped at 200 with explicit truncation. get_tree answers SHAPE, find answers IDENTITY - use find when y... |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_tree` | | (deviceName, container; optional path, nested=true, includeItems=false, depth=0 unlimited (1 = immediate children), typeFilter, limit) ONE-call tree view: folders with item/subfolder counts, plus contained items when includeItems=true. Item lines use ONE envelope on every container: Name  [key=value, key=value] with keys lowercase, comma-space separated, inapplicable keys omitted. Keys per cont... |
| &nbsp;&nbsp;&nbsp;&nbsp;`rename_folder` | | (deviceName, container, path, newName) NOT SUPPORTED by Openness V19: the folder name is read-only in the API, so this returns Error: RenameFailed. There is no move API either; rename folders in the TIA Portal UI. |
| **`get_info`** | **-** | **Server information** |
| **`hardware`** | **14** | **Hardware configuration** |
| &nbsp;&nbsp;&nbsp;&nbsp;`compile_all` | | Compile all HW. M4: 'Not compilable' = nothing needed recompiling (not an error). |
| &nbsp;&nbsp;&nbsp;&nbsp;`compile_device` | | (deviceName) F1: NullReferenceException — use compile_all instead. |
| &nbsp;&nbsp;&nbsp;&nbsp;`dump_catalog` | | Dump full TIA Portal hardware catalog (~11K entries) to local DB. Runs automatically on first TIA connection or TIA version change. Manual trigger for HSP updates. |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_csv` | | (outputPath) Device list to CSV. After export, ask the user if they want to open the file (use admin action=open_file). |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_hardware_map` | | (format:csv\|xlsx; optional deviceName, outputPath) Export raw CAx/AML hardware data for a device as flat table. Shows everything Siemens exports: rack, CPU, sub-items, signal modules, channels, addresses, tags. Single sheet. Defaults to exports_root/hardware/. Filename: {ProjectName}_{PLCName}_hardware_map_{timestamp}.{ext}. After export, ask the user if they want to open the file (use admin ac... |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_io_map` | | (format:csv\|xlsx; optional deviceName, outputPath) Export complete device I/O map combining rack/slot topology with tag assignments. XLSX: one sheet per slot + summary. CSV: flat combined. Omit deviceName to export all devices (one file each). Defaults to exports_root/hardware/. Filename: {ProjectName}_{PLCName}_io_map_{timestamp}.{ext}. After export, ask the user if they want to open the file ... |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_xlsx` | | (outputPath) Device list to Excel. After export, ask the user if they want to open the file (use admin action=open_file). |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_device` | | (deviceName) Detailed config for one device. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_full_config` | | Complete hardware config for all devices. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_io_map` | | Full I/O address map across all devices/modules. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_network` | | Subnets, nodes, IO systems across project. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_rack_slot_details` | | (deviceName) Rack/slot topology with I/O address assignments. |
| &nbsp;&nbsp;&nbsp;&nbsp;`search_catalog` | | (filter) Live real-time search of TIA Portal hardware catalog. Requires TIA Portal running. Capped at 50 results. |
| &nbsp;&nbsp;&nbsp;&nbsp;`set_network_config` | | (deviceName, confirm; optional ipAddress, subnetMask, useRouter, routerAddress, ipProtocolSelection, useIsoProtocol, pnDeviceNameAutoGeneration, stationName, pnDeviceName) Set network config on a device's PROFINET interface. pnDeviceName sets the PROFINET device name directly (auto-disables auto-generation). stationName renames the device. Modifies project config only — download to PLC required... |
| **`library`** | **25** | **Library management** |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_to_multi_instance_fb` | | (deviceName, parentFB, memberName, path; optional library='project', version, onCollision=fail\|replace) Resolve library type + delegate to blocks_write add_multi_instance_member. Type must be seeded via instantiate_library_type first. |
| &nbsp;&nbsp;&nbsp;&nbsp;`archive_global_library` | | (libraryName; optional targetDirectory, targetName, mode=Compressed) Create .zal19 archive. targetDirectory defaults to configured archives root — do NOT pass targetDirectory unless user explicitly specifies a different path. If archives root is not configured, direct user to open_manager → Paths tab to set it. |
| &nbsp;&nbsp;&nbsp;&nbsp;`close_global_library` | | (libraryName) Close loaded global library. |
| &nbsp;&nbsp;&nbsp;&nbsp;`compare_to_target` | | (targetLibrary; optional library='project', includeIdentical=false) Compare libraries: only_in_source, only_in_target, differs, identical. Read-only. |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_global_library` | | (name; optional targetDirectory) Create new empty .al19. targetDirectory defaults to configured libraries root — do NOT pass targetDirectory unless user explicitly specifies a different path. If libraries root is not configured, direct user to open_manager → Paths tab to set it. Opens in ReadWrite. Call save_global_library to persist. |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_library_folder` | | (folderPath; optional library='project') Create subfolder (idempotent, auto-creates intermediates). |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_library_folder` | | (folderPath; optional library='project', recursive=false) Delete subfolder. Non-empty refused unless recursive=true. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_master_copy` | | (path; optional library='project') Delete by full slash-path. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_unused_types` | | (optional library='project', path, mode=preserve_default\|delete_all, dryRun=true, confirm) Remove types with zero instances. dryRun=true default; set false + confirm to execute. |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_type_xml` | | (library, path, outputPath; optional version, overwrite=false) Export type version to XML file. After export, ask the user if they want to open the file (use admin action=open_file). |
| &nbsp;&nbsp;&nbsp;&nbsp;`find` | | (library, query; optional path, typeFilter:all\|types\|master_copies, caseSensitive=false, limit) Glob search (* and ?). Recursive walk, cap 500 matches with loud NOTICE on truncation; matches carry the same envelope as get_tree. Scope with path on huge libraries. section was REMOVED (use typeFilter). |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_info` | | (library) Metadata: edit mode, version, author, item counts, root folders. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_master_copy` | | (library, path) Master copy metadata: name, author, date, comment, wrapped kind. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_type` | | (library, path; optional version='latest_committed', includeXml=false) Type metadata + version list. includeXml=true inlines XML body (cap ~30KB). |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_tree` | | (library; optional path (default root), depth=0 unlimited (1 = immediate children), nested=true, includeItems=false, typeFilter:all\|types\|master_copies, limit) Recursive subtree view - args, defaults and depth rule now IDENTICAL to folders get_tree (v3.0.0). nested=false = flat path list (the machine contract). Cap 1000 items, loud NOTICE on truncation. Type lines carry the uniform envelope [ve... |
| &nbsp;&nbsp;&nbsp;&nbsp;`instantiate_library_type` | | (deviceName, path; optional library='project', version='latest_committed') Instantiate type version into device blocks. Only Committed versions. Side effect: seeds project.ProjectLibrary. |
| &nbsp;&nbsp;&nbsp;&nbsp;`instantiate_master_copy` | | (deviceName, path; optional library='project') Materialize master copy into device blocks. |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_libraries` | | All libraries (project + loaded globals). Returns names for 'library' arg. Reserved name 'project' = project library. |
| &nbsp;&nbsp;&nbsp;&nbsp;`open_global_library` | | (optional path; optional openMode=ReadOnly\|ReadWrite) Load .al19 file. Without path, lists available libraries from configured libraries root. |
| &nbsp;&nbsp;&nbsp;&nbsp;`promote_to_global` | | (targetLibraryName; optional sourceLibrary='project', path, forceUpdateMode, structureConflictMode, deleteUnusedVersions) Copy types to writable global library. |
| &nbsp;&nbsp;&nbsp;&nbsp;`publish_block_as_master_copy` | | (deviceName, blockName; optional library='project', folderPath='', onCollision=autoRename\|replace\|fail) Publish FB/FC/OB/GlobalDB as master copy. Works on inconsistent blocks. |
| &nbsp;&nbsp;&nbsp;&nbsp;`publish_plc_type_as_master_copy` | | (deviceName, udtName; optional library, folderPath, onCollision) Same as above for UDTs. |
| &nbsp;&nbsp;&nbsp;&nbsp;`save_global_library` | | (libraryName) Persist open global library to .al19. |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_check` | | (optional library='project', path, mode=ReportOutOfDateOnly) Preview what update_project would change. Read-only. |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_project` | | (optional library='project', path, deleteUnusedVersions=false) DESTRUCTIVE: apply library-type updates to all matching blocks. Run update_check first. |
| **`live_data`** | **11** | **Live PLC values + diagnostics** |
| &nbsp;&nbsp;&nbsp;&nbsp;`connect` | | (host; optional password) Attach to a PLC over an encrypted connection (TCP port 102). host = PLC IP. password only if the CPU access level requires it (never logged/echoed). If password is omitted and one was stored via set_credential for this host, it is used automatically. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_credential` | | (host; optional passwordClass) Remove stored credential(s) for a host. |
| &nbsp;&nbsp;&nbsp;&nbsp;`disconnect` | | Release the live-data session. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_status` | | Live-data readiness, connection state, PLC identity. Safe anytime. |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_credentials` | | List stored credentials (host, class, label, timestamps). NEVER shows the secret. |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_readable` | | (optional root, limit) What the PLC actually exposes for reading, by root. No argument = one line per root with leaf counts; root=MArea (or a DB name) lists that root's leaves. Use this when a tag comes back unresolved - it separates 'not declared' from 'whole area empty'. |
| &nbsp;&nbsp;&nbsp;&nbsp;`read_db` | | (dbName; optional members[]) Live values of a data block (optimized or standard), symbolic, with quality codes. members[] limits to specific members. |
| &nbsp;&nbsp;&nbsp;&nbsp;`read_diagnostics` | | (optional kind=all\|identity\|protection\|alarms\|buffer, maxEntries) CPU order number + firmware (online - Openness CANNOT read this), protection level, active + configured alarms, and the CPU diagnostic buffer (kind=buffer or all): the same event log TIA Portal shows under Online and Diagnostics, newest first, timestamped from the CPU clock in UTC. Event text is resolved locally from a catalog, s... |
| &nbsp;&nbsp;&nbsp;&nbsp;`read_tag_table` | | (deviceName, tagTableName; optional tags[]) Live values for a PLC tag table. Tags may be named symbolically or by absolute address (%M1.0, %MB1, %MW20, %MD4, and the %I/%Q equivalents). Blocks if the named device's order number differs from the connected CPU's. |
| &nbsp;&nbsp;&nbsp;&nbsp;`read_watch_table` | | EITHER (symbols[]) for fully-qualified symbols, OR (deviceName, watchTableName) to resolve a project watch table and read its entries live. Passing both is an error. |
| &nbsp;&nbsp;&nbsp;&nbsp;`set_credential` | | (host, password; optional passwordClass, label) Store a CPU password ENCRYPTED at rest (machine-bound), so connect can use it without passing the password each time. The secret is never echoed or logged. |
| **`open_manager`** | **-** | **Open Manager window** |
| **`session`** | **16** | **Session management** |
| &nbsp;&nbsp;&nbsp;&nbsp;`archive` | | (optional outputDirectory; optional archiveName, timestamp, archivationMode, saveChanges) Create .zapXX archive. outputDirectory defaults to configured archives root — do NOT pass outputDirectory unless user explicitly specifies a different path. If archives root is not configured, direct user to open_manager → Paths tab. timestamp=true default. archivationMode: None\|Compressed\|DiscardRestorabl... |
| &nbsp;&nbsp;&nbsp;&nbsp;`close_project` | | (optional saveChanges) Close project. saveChanges: prompt\|save\|discard\|auto. |
| &nbsp;&nbsp;&nbsp;&nbsp;`configure` | | (optional projectsRoot, archivesRoot, exportsRoot, librariesRoot, httpEnabled) Set default folder paths for create/open/archive/exports/libraries. Validates directory exists (exportsRoot auto-creates if missing). Use open_manager GUI as alternative. ALSO (#168): preferredTiaBinding=<version> is the TIA Portal SELECTOR - on a PC with several TIA Portals installed, pick which one TiaCommander bin... |
| &nbsp;&nbsp;&nbsp;&nbsp;`connect` | | (optional withUI=true) Attach to running TIA Portal, auto-loads first project. Releases any stale handle first. |
| &nbsp;&nbsp;&nbsp;&nbsp;`create` | | (name; optional parentDirectory, withUI=true) Create new project. Self-contained: auto-launches TIA Portal or attaches to a running instance, cleans up stale handles. ASCII names only (M12). parentDirectory defaults to configured projects root — do NOT pass parentDirectory unless user explicitly specifies a different path. If projects root is not configured, direct user to open_manager → Paths ... |
| &nbsp;&nbsp;&nbsp;&nbsp;`disconnect` | | (optional saveChanges, closeTia) Release Openness handle. closeTia=true also terminates TIA process. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_project` | | Name, path, version, device count of open project. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_state` | | Proactive state check. Returns state level (0-4), TIA/project/device status, license info, and restart flag. Call at session start. Works at any state. |
| &nbsp;&nbsp;&nbsp;&nbsp;`info` | | Server version, tool count, workflow hint. |
| &nbsp;&nbsp;&nbsp;&nbsp;`is_dirty` | | Returns true/false for unsaved changes. |
| &nbsp;&nbsp;&nbsp;&nbsp;`launch` | | (optional withUI=true) Start new TIA Portal (no project). Releases any stale handle first. Prefer create/open which auto-launch. |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_archives` | | (optional archivesRoot) List all .zap* archive files from configured or explicit archives root. Shows filename, size, date, TIA version. |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_devices` | | All devices in project. Provides deviceName for other tools. |
| &nbsp;&nbsp;&nbsp;&nbsp;`open` | | (optional projectPath; optional withUI=true) Open project, auto-launch TIA if needed. Idempotent. Without projectPath, lists available projects from configured projects root. |
| &nbsp;&nbsp;&nbsp;&nbsp;`save` | | Save project. OFFLINE REQUIRED: refuses with OnlineModeRestriction while any device is online — run diagnostics go_offline first. |
| &nbsp;&nbsp;&nbsp;&nbsp;`save_as` | | (newName; optional newParentDirectory) Save copy at new location. newParentDirectory defaults to configured projects root if set. |
| **`tag`** | **13** | **PLC tag management** |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_tag` | | (deviceName, tagTableName, tagName, dataType; optional logicalAddress, comment) Add tag. dataType: Bool, Int, Real, etc. logicalAddress: %%M0.0, %%MW100. |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_table` | | (deviceName, tagTableName; optional targetFolder, createParents=false) New empty table. targetFolder ('Motors/Drives' syntax) creates it directly inside that folder in the PLC tags tree; missing folders are an ERROR unless createParents=true. Name collision is checked recursively across all folders. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_table` | | (deviceName, tagTableName) Delete table and all tags. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_tag` | | (deviceName, tagTableName, tagName) Delete single tag. |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_tag_table_data` | | (deviceName, tagTableName, format:csv\|xlsx\|xml; optional outputPath) Export tag table to CSV or XLSX for documentation/analysis, or to raw TIA XML (format=xml) for round-trip/import. Columns: Name, DataType, Address, Comment, ExternalAccessible, ExternalVisible, ExternalWritable. Defaults to exports_root\tag\ if no outputPath. After export, ask the user if they want to open the file (use admin ... |
| &nbsp;&nbsp;&nbsp;&nbsp;`find_next_free` | | (deviceName; optional area:M\|I\|Q, startByte:int, dataType:Bool\|Byte\|Word\|DWord\|Int\|Real\|etc., mode:declared\|used) Find next free address. mode=declared (default, safest): skips all declared tag addresses. mode=used: skips only addresses referenced in code. Alignment: Bool=any bit, Byte=byte, Word=even byte, DWord=4-byte. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_assignment_list` | | (deviceName; optional area:M\|I\|Q\|all, mode:declared\|used\|conflicts) Address occupancy for M/I/Q areas + HW I/O. mode=declared (default): all tag addresses. mode=used: only addresses referenced in block code (mirrors TIA Assignment List). mode=conflicts: addresses used in 2+ blocks with Read/Write detail — detects double assignments. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_table_details` | | (deviceName, tagTableName) All tags with types, addresses and comments. |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_tables` | | (deviceName) All tag tables, one line each with qualified 'Folder/Sub/Name' path and the uniform envelope [type=PlcTagTable, tags=N]. Totals reconcile with folders get_tree (container=tag_tables). |
| &nbsp;&nbsp;&nbsp;&nbsp;`move_table` | | (deviceName, tagTableName, targetFolder; optional createParents=false) OFFLINE REQUIRED. Move a tag table between folders ('' = container root). Emulated as export -> delete -> import into the target folder (spike-proven full fidelity); restore-on-failure back to the original location. NOTE: tag tables cannot be COPIED - PLC tag names are global across all tables. |
| &nbsp;&nbsp;&nbsp;&nbsp;`search` | | (deviceName, tagName) Substring match across all tables. |
| &nbsp;&nbsp;&nbsp;&nbsp;`set_access` | | (deviceName, tagTableName, tagName, accessible, visible, writable) CRITICAL: args are accessible/visible/writable, NOT externalAccessible etc. |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_comment` | | (deviceName, tagTableName, tagName, newComment) CRITICAL: arg is 'newComment', NOT 'comment'. |
| **`udt`** | **11** | **User-defined type management** |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_member` | | (deviceName, udtName, memberName, dataType; optional comment, initialValue) Add member via XML round-trip. |
| &nbsp;&nbsp;&nbsp;&nbsp;`create` | | (deviceName, udtName) New empty UDT (uses SCL ExternalSources workaround internally - creates at the ROOT of PLC data types only). For a foldered UDT: create at root, then action=move. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete` | | (deviceName, udtName) Delete UDT. Rejects if still referenced. |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_member` | | (deviceName, udtName, memberName) Remove member via XML round-trip. |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_xml` | | (deviceName, udtName, outputPath) Export UDT to XML file. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_structure` | | (deviceName, udtName) Full member list with types, comments, access flags via XML. |
| &nbsp;&nbsp;&nbsp;&nbsp;`import_xml` | | (deviceName, filePath OR xmlContent; optional overwrite) Import UDT from XML. filePath reads from disk; xmlContent accepts inline XML string (no file tool needed). Reliable for round-trips; may fail on synthesized XML (use create+add_member instead). |
| &nbsp;&nbsp;&nbsp;&nbsp;`list` | | (deviceName) All UDTs, one line each with qualified 'Folder/Sub/Name' path, the uniform envelope [type=PlcStruct, num=N] and the modified date. |
| &nbsp;&nbsp;&nbsp;&nbsp;`move` | | (deviceName, udtName, targetFolder; optional createParents=false) OFFLINE REQUIRED. Move a UDT between folders ('' = container root). Emulated as export -> delete -> import into the target folder (spike-proven); restore-on-failure back to the original location. |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_member` | | (deviceName, udtName, memberName; optional newDataType, newInitialValue, newComment) Update member properties. At least one field required. Uses SCL round-trip. |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_member_comment` | | (deviceName, udtName, memberName, newComment) CRITICAL: arg is 'newComment', NOT 'comment'. |
| **`watch`** | **14** | **Watch & force tables** |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_entry` | | (deviceName, target, tableName, address; optional displayFormat, modifyValue[watch], forceValue[force], modifyTrigger[watch], monitorTrigger, comment). IMPORTANT: always set displayFormat to match the source variable's data type — Bool→Bool, Int/Word→DEC_signed, DInt/DWord→DEC_signed, Real→Float, Time→Time, String→String. Never leave it as default Hex. Look up the data type from the source bloc... |
| &nbsp;&nbsp;&nbsp;&nbsp;`clear_table` | | (deviceName, watchTableName) Remove all entries, keep table. |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_table` | | (deviceName, watchTableName; optional targetFolder, createParents=false) New empty watch table. targetFolder creates it directly inside that folder in the watch/force tables tree; missing folders are an ERROR unless createParents=true. Name collision checked recursively (watch AND force tables). |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_entry` | | (deviceName, target, watchTableName\|forceTableName, address) Remove entry. ADDRESS FORM: pass the bare absolute address (%%M60.0) OR the exact symbolic name. TIA stores a tag-owned address SYMBOLICALLY, so the bare form is resolved via the tag tables when no entry matches it literally, and the result says 'matched symbolically as ""TagName""'. If the bare address is owned by more than one tag t... |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_table` | | (deviceName, watchTableName) Delete watch table. |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_force_data` | | (deviceName, forceTableName, format:csv\|xlsx; optional outputPath) Export force table to CSV or XLSX for documentation. Defaults to exports_root\watch\. After export, ask the user if they want to open the file (use admin action=open_file). |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_table` | | (deviceName, target, tableName, outputPath) Export to XML. After export, ask the user if they want to open the file (use admin action=open_file). |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_watch_data` | | (deviceName, watchTableName, format:csv\|xlsx; optional outputPath) Export watch table to CSV or XLSX for documentation. Defaults to exports_root\watch\. After export, ask the user if they want to open the file (use admin action=open_file). |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_entries` | | (deviceName, target, watchTableName\|forceTableName) List entries. |
| &nbsp;&nbsp;&nbsp;&nbsp;`import_table` | | (deviceName, filePath OR xmlContent; optional overwrite) Import from XML. filePath reads from disk; xmlContent accepts inline XML string (no file tool needed). |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_tables` | | (deviceName) All watch and force tables, one line each with qualified 'Folder/Sub/Name' path and the uniform envelope [type=PlcWatchTable\|PlcForceTable, entries=N, force=true\|false]. Totals reconcile with folders get_tree (container=watch_tables). |
| &nbsp;&nbsp;&nbsp;&nbsp;`move_table` | | (deviceName, watchTableName, targetFolder; optional createParents=false) OFFLINE REQUIRED. Move a watch/force table between folders ('' = container root). Emulated as export -> delete -> import into the target folder; restore-on-failure back to the original location. |
| &nbsp;&nbsp;&nbsp;&nbsp;`rename_table` | | (deviceName, watchTableName, newName) Rename (XML round-trip internally). |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_entry` | | Same params as add_entry. Cannot clear fields (delete+re-add instead). Accepts either address form — see delete_entry. |
| **`xref`** | **6** | **Cross-reference analysis** |
| &nbsp;&nbsp;&nbsp;&nbsp;`find_call_paths` | | (deviceName, blockName; optional maxDepth=10) All call paths from OB roots to the target block over CALL edges, e.g. Main [OB1] -> ModbusPoll [FB2] -> mb_query [FB8]. Complements find_callers (find_callers = direct callers only; this = full qualified paths from the OBs). blockName accepts a bare name (resolved recursively across all folders) or a folder-qualified path. |
| &nbsp;&nbsp;&nbsp;&nbsp;`find_callers` | | (deviceName, blockName) Which blocks call/reference the target block. Iterates every block. |
| &nbsp;&nbsp;&nbsp;&nbsp;`find_orphaned_instance_dbs` | | (deviceName) Instance DBs whose owning FB is missing or has no TypeInstance/InstanceType reference. |
| &nbsp;&nbsp;&nbsp;&nbsp;`find_unused` | | (deviceName) All blocks not called from any OB chain. Iterates every block (~5-10ms each). |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_call_tree` | | (deviceName; optional blockName, maxDepth=10) Forward CALL tree: what a root calls, recursively to any depth (default roots: all OBs; blockName accepts bare or folder-qualified name). Each node shows folder-qualified name, block number, call site network, instance DB; system instructions appear as leaves. Cycle-safe; a subtree already shown is collapsed with a marker. |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_references` | | (deviceName, blockName; optional filter:AllObjects\|ObjectsWithReferences\|ObjectsWithoutReferences\|UnusedObjects) Cross-references for one block. Shows referenced objects with access type (Read/Write/Call etc.) and location. |
**Total: 18 tools, 205 actions** — action descriptions are abbreviated in this table; the full text for every action is in the machine-readable tool list (`data/tool-list/tools.json`).

Each meta-tool groups related actions behind a single `action` parameter. `get_info` and `open_manager` are standalone utilities that work without a license. Start with `session` to connect, then use any other tool.

---

## Tested Hardware

TiaCommander should work with any PLC supported by TIA Portal and the Openness API. The following devices have been tested in-house with full tool coverage:

| Device | Order Number | Firmware | TIA Portal |
|---|---|---|---|
| S7-1200 CPU 1212C DC/DC/Rly | 6ES7 212-1BE31-0XB0 | V3.0 | V19 |
| S7-1200 CPU 1214C DC/DC/DC | 6ES7 214-1AG31-0XB0 | V3.0 | V19 |
| S7-1200 CPU 1214C DC/DC/DC | 6ES7 214-1HG40-0XB0 | V4.5 | V19 |
| S7-1200 CPU 1215C DC/DC/DC | 6ES7 215-1AG31-0XB0 | V3.0 | V19 |
| S7-1200 CPU 1215C DC/DC/Rly | 6ES7 215-1BG31-0XB0 | V3.0 | V19 |
| S7-1200 CPU 1217C DC/DC/DC | 6ES7 217-1AG40-0XB0 | V4.2 | V19 |

**See what the community is actually running — [tiacommander.com/stats](https://tiacommander.com/stats).** The live compatibility list shows every device reported working across all users, with its order number, firmware and the TIA Portal versions it was seen under, alongside community tool usage and AI client adoption. It is built from anonymised, aggregated telemetry and is a far broader picture than the in-house table above.

---

## Block Creation

TiaCommander creates complete PLC blocks from structured JSON descriptions:

- **SCL blocks** — via External Source API (firmware-agnostic)
- **LAD/FBD blocks** — via FlgNet XML builder with 69 verified instruction types:
  - Bit logic, timers (TON/TOF/TP), counters (CTU/CTD/CTUD)
  - Math (Add, Sub, Mul, Div, Mod, Abs, Sqrt, Sin, Cos, etc.)
  - Comparators (EQ, NE, GT, GE, LT, LE)
  - Move & conversion, word logic, shift & rotate, selection
  - Program flow (FB/FC calls with parameter wiring, JMP/JMPN, RET, LABEL)
- **Block numbers** — auto-assigned if omitted, or explicit
- **Custom interfaces** — Input, Output, InOut, Static, Temp, Constant sections

---

## Safety

The MCP server treats PLC deployment operations as destructive. These actions require explicit confirmation strings before execution:

| Action | What it does |
|---|---|
| `download_to_device` | Downloads software/hardware to a live PLC |
| `upload_station` | Uploads a PLC station into the project |
| `delete_unused_types` | Removes library types with zero instances |

All other operations work within the TIA Portal project and are reversible before download.

---

## Standalone I/O

TiaCommander works fully standalone — no external file management tools required.

**Three input modes** for every import: file path, inline content, or export database reference.

**Three output modes** for every export: inline (default), export database, or file.

Exports auto-expire after 24 hours. Manage via `admin` tool actions.

However, we strongly recommend pairing TiaCommander with [Desktop Commander](https://desktopcommander.app/) ([GitHub](https://github.com/wonderwhy-er/DesktopCommanderMCP)) for your daily AI agent workflow. Desktop Commander gives your AI assistant terminal access, file management, and code editing capabilities across your entire OS. This is not a paid promotion — we use Desktop Commander every day in our own work, and it happens to be created by our fellow Latvian, Eduards Ruzga. It's free, open-source, and it makes everything better.

---

## Data & Privacy

All data stays on your machine. The local SQLite database stores tool call statistics, device profiles, error log, and temporary export cache.

TiaCommander collects limited telemetry to improve the product:

- **Error reports** — when a tool call fails, the error type and context are sent so we can identify and fix issues.
- **Tool usage statistics** — which tools and actions are used most often, to understand what is most valuable.
- **AI client information** — which AI assistant is connected (client name and version), to ensure compatibility.

No PLC program data, project files, or block contents are ever transmitted.

Your personal information (name, surname, email) provided during registration is governed by European Union GDPR rules. We do not share, sell, or disclose your data to third parties. We will never send you marketing materials or anything not directly relevant to your use of TiaCommander — unless you explicitly ask us to.

---

## What's Next

TiaCommander is actively developed. Here's what we're working on:

- **HMI integration** — access to HMI screens, tags, and alarm views
- **Wider live-data coverage** — more of what a running PLC exposes, across more CPU families
- **Simulation support** — drive PLCSIM for offline development and testing
- **Broader hardware validation** — more device families and firmware verified in-house, guided by real-world usage


Have a feature idea? [Submit it on GitHub](https://github.com/a4webdev/tiacommander-mcp/issues).

---

## Quick Start

**Claude Desktop (one-click):** Download [`TiaCommander.mcpb`](https://github.com/a4webdev/tiacommander-mcp/releases/latest) from Releases, then install via Settings -> Extensions -> Advanced Settings -> Install Extension. Done.

**All other AI clients:** **[Download the portable ZIP](https://github.com/a4webdev/tiacommander-mcp/releases/latest)** and see the **[Quick Start Guide](docs/1-QUICKSTART.md)** -- everything you need to get up and running in 5 minutes: extract, Openness group setup, AI client configuration for all 7 tested clients, license activation, and first commands.

<!-- TODO #132: Add YouTube installation video link here -->

Ready-to-use config files for each AI client are in the `configs/` folder.

---

## License & Feedback

TiaCommander is proprietary software published by SIA A4 Studio. During the beta period, the tool is **completely free** with **unlimited usage** and **all tools available** — no restrictions.

In return, we count on you to help us make TiaCommander better. If you encounter a bug, have an idea for a new feature, or just want to share how you use the tool in your workflow — we want to hear from you:

- **Bug reports:** [github.com/a4webdev/tiacommander-mcp/issues](https://github.com/a4webdev/tiacommander-mcp/issues)
- **Feature requests:** [github.com/a4webdev/tiacommander-mcp/issues](https://github.com/a4webdev/tiacommander-mcp/issues)
- **Testimonials & feedback:** [tiacommander.com/wf/github](https://tiacommander.com/wf/github)

Every report, every suggestion, every comment helps us build a better tool for the Siemens automation community.

See `LICENSE.txt` for full terms. Visit [tiacommander.com](https://tiacommander.com) for more information.

---

## Trademarks

TiaCommander™ is a trademark of SIA A4 Studio. Siemens, TIA Portal, SIMATIC, S7-1200, S7-1500 are trademarks of Siemens AG. TiaCommander is not affiliated with, endorsed by, or sponsored by Siemens AG.

---

## Thank You

Thank you to everyone who has opened an issue or sent us feedback — it genuinely helps us make TiaCommander better, and we read every word. And if your experience has been smooth and there is nothing to report, we would be grateful if you left a star on the repository — it is a small thing, and it helps other Siemens engineers find the tool.