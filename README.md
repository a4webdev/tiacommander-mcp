# TiaCommander — MCP Server for Siemens TIA Portal

| | |
|---|---|
| **Version** | 2.25.0 |
| **Protocol** | MCP 2024-11-05 (stdio) |
| **Runtime** | .NET Framework 4.8 |
| **TIA Portal** | V15.1 – V21* (Openness API, tested on V19) |
| **License** | Proprietary (free beta) |

*\*V15.1–V18 and V20–V21 are expected to work as TIA Portal maintains backward-compatible Openness APIs across versions, but have not been tested. Only V19 is fully tested.*

---

## Overview

TiaCommander is an MCP server that connects AI assistants to Siemens TIA Portal via the Openness API. It enables AI-assisted PLC programming, project management, library operations, hardware configuration, cross-reference analysis, and deployment — all through natural language.

**Tested AI clients:** Claude Desktop, Claude Code, VS Code (Copilot), Cursor, Windsurf, Codex CLI, Gemini CLI.

**16 tools, 166 actions** covering the full TIA Portal project lifecycle.

---

## What's in the download?

```
TiaCommander-v2.25.0-portable/
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
│   └── README.md
├── data/
│   └── exports/                       ← Export output folders
│       ├── alarm_text/
│       ├── hardware/
│       ├── tag/
│       └── watch/
├── help/
│   ├── github-offline.html
│   └── readme-offline.html
├── Resources/
│   ├── logoBlack.png
│   └── tiacommander.png
├── runtimes/                          ← WebView2 native loaders
├── Templates/                         ← Block XML + alarm templates
├── x64/                               ← SQLite native library (64-bit)
├── x86/                               ← SQLite native library (32-bit)
├── CHANGELOG.md
├── EPPlus.dll
├── EPPlus.Interfaces.dll
├── LICENSE.txt
├── Microsoft.Bcl.AsyncInterfaces.dll
├── Microsoft.IO.RecyclableMemoryStream.dll
├── Microsoft.Web.WebView2.Core.dll
├── Microsoft.Web.WebView2.WinForms.dll
├── QUICKSTART.md
├── README.md
├── Siemens.Engineering.dll
├── Siemens.Engineering.Hmi.dll
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
├── TiaCommander.exe                   ← MCP server + Manager GUI
└── TiaCommander.exe.config
```

No installer, no registry changes, no system modifications. Extract, configure, and run. Download the latest `.zip` from [GitHub Releases](https://github.com/a4webdev/tiacommander-mcp/releases).

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
  16 tools · 166 actions · standalone I/O · .NET Framework 4.8
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
| **`open_manager`** | — | **Launch Manager GUI for registration, license, configuration** |
| **`get_info`** | — | **Server version, tool count, license status** |
| **`session`** | **15** | **Entry point — connect to TIA Portal, manage projects** |
| &nbsp;&nbsp;&nbsp;&nbsp;`connect` | | Attach to running TIA Portal (withUI=true by default) |
| &nbsp;&nbsp;&nbsp;&nbsp;`launch` | | Start new TIA Portal instance (withUI=true by default) |
| &nbsp;&nbsp;&nbsp;&nbsp;`disconnect` | | Release handle, optionally close TIA |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `closeTia` | | Also terminate TIA Portal process |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `saveChanges` | | prompt / save / discard / auto |
| &nbsp;&nbsp;&nbsp;&nbsp;`open` | | Open .ap19 project (auto-launch TIA if needed, idempotent) |
| &nbsp;&nbsp;&nbsp;&nbsp;`close_project` | | Close with save/discard/auto |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_project` | | Current project name, path, version |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_devices` | | Enumerate devices — needed by all other tools |
| &nbsp;&nbsp;&nbsp;&nbsp;`save` | | Save project |
| &nbsp;&nbsp;&nbsp;&nbsp;`is_dirty` | | Check unsaved changes |
| &nbsp;&nbsp;&nbsp;&nbsp;`create` | | Create new project (withUI=true by default) |
| &nbsp;&nbsp;&nbsp;&nbsp;`save_as` | | Save under new name/location |
| &nbsp;&nbsp;&nbsp;&nbsp;`archive` | | Export .zap19 archive |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `archivationMode` | | None / Compressed / DiscardRestorableData |
| &nbsp;&nbsp;&nbsp;&nbsp;`configure` | | Set default paths (projects, archives, exports, libraries) |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_archives` | | List .zap* archives from configured root |
| **`blocks_read`** | **14** | **Inspect, compile, export blocks** |
| &nbsp;&nbsp;&nbsp;&nbsp;`list` | | List all blocks with type/number/language |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `typeFilter` | | all / code / OB / FB / FC / DB / GlobalDB / InstanceDB |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `includeInstanceDbs` | | Expand instance DB roll-up |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_details` | | Block info, size, timestamps |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_interface` | | Full interface structure (In/Out/Static/Temp) |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_all_interfaces_summary` | | Summary across all blocks |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_xml_raw` | | Raw XML content inline |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_xml_inline` | | Parsed XML structure inline |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_xml_file` | | Export XML to file or inline (returnInline) |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_source` | | Export SCL/STL source |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_all_xml` | | Export all blocks to directory or inline |
| &nbsp;&nbsp;&nbsp;&nbsp;`compile_block` | | Compile single block |
| &nbsp;&nbsp;&nbsp;&nbsp;`compile_all` | | Compile all blocks |
| &nbsp;&nbsp;&nbsp;&nbsp;`check_consistency` | | Block consistency status |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_compiler_messages` | | All compiler messages |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_compiler_errors` | | Errors only |
| **`blocks_write`** | **15** | **Create, delete, import, modify blocks** |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_fb` | | Create function block (number auto-assigned if omitted) |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_fc` | | Create function (number auto-assigned if omitted) |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_ob` | | Create organization block (number auto-assigned if omitted) |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_block` | | Full block creation with interface + logic |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `blockType` | | FB / FC / OB |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `language` | | LAD / SCL / FBD / STL (69 LAD/FBD instruction types) |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `memoryLayout` | | Optimized (default) / Standard |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `interface` | | Custom In/Out/InOut/Static/Temp/Constant sections |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `networks[]` | | Multi-network blocks with per-network SCL/title/comment |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_block` | | Delete any block (optional force for orphaned instance DBs) |
| &nbsp;&nbsp;&nbsp;&nbsp;`import_xml_inline` | | Import block from XML string |
| &nbsp;&nbsp;&nbsp;&nbsp;`import_xml_file` | | Import block from file or export DB (exportId) |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_multi_instance_member` | | Add static member to FB (bulk: members[]) |
| &nbsp;&nbsp;&nbsp;&nbsp;`remove_multi_instance_member` | | Remove static member from FB |
| &nbsp;&nbsp;&nbsp;&nbsp;`rename_block` | | Rename block and/or change number |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_network` | | Update network title/comment |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_network_element` | | Surgical edit of Call elements |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `newTarget` | | Retarget to different FB/FC |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `newInstance` | | Swap instance DB |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `pinWires` | | Rewire input/output pins to tags |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_network` | | Insert empty network at position |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_network` | | Remove network by index |
| &nbsp;&nbsp;&nbsp;&nbsp;`replace_network` | | Replace network title/comment |
| **`db`** | **9** | **Data block operations** |
| &nbsp;&nbsp;&nbsp;&nbsp;`list` | | List all global and instance DBs |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_structure` | | Full DB structure via XML parse |
| &nbsp;&nbsp;&nbsp;&nbsp;`create` | | Create new global DB (number auto-assigned if omitted) |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_instance_db` | | Create instance DB for an FB |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete` | | Delete data block |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_member` | | Add member to global DB (bulk: members[]) |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_member` | | Remove member from global DB (bulk: memberNames[]) |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_member` | | Update member type, start value, comment, access flags |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_member_comment` | | Update member comment only |
| **`tag`** | **12** | **Tag tables, tags, assignment list** |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_tables` | | List all tag tables |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_table_details` | | Tags in a table with addresses/types |
| &nbsp;&nbsp;&nbsp;&nbsp;`search` | | Search tags by name (partial match) |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_table` | | Create new tag table |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_table` | | Delete tag table |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_tag` | | Add tag to table (bulk: tags[]) |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_tag` | | Remove tag from table (bulk: tagNames[]) |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_comment` | | Update tag comment |
| &nbsp;&nbsp;&nbsp;&nbsp;`set_access` | | Set external access flags (accessible, visible, writable) |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_assignment_list` | | Address occupancy for M/I/Q areas + HW I/O |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `mode=declared` | | All declared tag addresses (default) |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `mode=used` | | Only addresses referenced in block code |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `mode=conflicts` | | Detect addresses in 2+ blocks (Q=error, M=info, I=hidden) |
| &nbsp;&nbsp;&nbsp;&nbsp;`find_next_free` | | Next free address with alignment (Bool/Byte/Word/DWord) |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `mode=declared` | | Skip all declared tag addresses (default, safest) |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `mode=used` | | Skip only code-referenced addresses |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_tag_table_data` | | Export tag table to CSV or XLSX |
| **`udt`** | **10** | **User-defined type operations** |
| &nbsp;&nbsp;&nbsp;&nbsp;`list` | | List all UDTs |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_structure` | | UDT member structure |
| &nbsp;&nbsp;&nbsp;&nbsp;`create` | | Create new UDT |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete` | | Delete UDT |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_member` | | Add member to UDT (bulk: members[]) |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_member` | | Remove member (bulk: memberNames[]) |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_member` | | Update member type, initial value, comment |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_member_comment` | | Update member comment |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_xml` | | Export UDT to XML (file or inline) |
| &nbsp;&nbsp;&nbsp;&nbsp;`import_xml` | | Import UDT from XML (filePath / xmlContent / exportId) |
| **`watch`** | **13** | **Watch and force table debugging** |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_tables` | | List all watch + force tables |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_table` | | Create watch table |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_table` | | Delete watch table |
| &nbsp;&nbsp;&nbsp;&nbsp;`rename_table` | | Rename watch table |
| &nbsp;&nbsp;&nbsp;&nbsp;`clear_table` | | Remove all entries |
| &nbsp;&nbsp;&nbsp;&nbsp;`import_table` | | Import from XML (filePath / xmlContent / exportId) |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_entries` | | List entries with addresses/formats |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_entry` | | Add watch/force entry |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_entry` | | Update entry fields |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_entry` | | Remove entry by address |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_table` | | Export to XML (file or inline) |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_watch_data` | | Export watch table to CSV or XLSX |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_force_data` | | Export force table to CSV or XLSX |
| **`hardware`** | **12** | **Hardware config, network, I/O** |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_device` | | Single device configuration |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_rack_slot_details` | | Rack/slot topology + I/O assignments |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_full_config` | | All devices configuration |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_network` | | Subnets, nodes, IO systems |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_io_map` | | Full I/O address map |
| &nbsp;&nbsp;&nbsp;&nbsp;`compile_device` | | Compile single device HW |
| &nbsp;&nbsp;&nbsp;&nbsp;`compile_all` | | Compile all HW |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_csv` | | Device list to CSV |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_xlsx` | | Device list to Excel |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_io_map` | | Full device I/O map (rack/slot + tags) to CSV or XLSX |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_hardware_map` | | Raw CAx/AML hardware data to CSV or XLSX |
| &nbsp;&nbsp;&nbsp;&nbsp;`set_network_config` | | Set IP, subnet, PROFINET name, station name |
| **`library`** | **26** | **Library types, master copies, lifecycle** |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_libraries` | | List project + open global libraries |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_info` | | Library details + counts |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_folder` | | Browse folder contents |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `section` | | types / master_copies / both |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_tree` | | Recursive tree dump |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `section` | | types / master_copies / both |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `nested` | | Flat list (default) or indented hierarchy |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `includeItems` | | false = folder structure only (fast orientation) |
| &nbsp;&nbsp;&nbsp;&nbsp;`find` | | Search by glob pattern (* and ?) |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `section` | | types / master_copies / both |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_type` | | Type details + version list |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `includeXml` | | Inline XML body (capped ~30KB) |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `version` | | latest_committed (default) / explicit / latest_any |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_master_copy` | | Master copy metadata |
| &nbsp;&nbsp;&nbsp;&nbsp;`publish_block_as_master_copy` | | Publish FB/FC/OB to library |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `onCollision` | | autoRename / replace / fail |
| &nbsp;&nbsp;&nbsp;&nbsp;`publish_plc_type_as_master_copy` | | Publish UDT to library |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_master_copy` | | Delete master copy |
| &nbsp;&nbsp;&nbsp;&nbsp;`instantiate_master_copy` | | Copy master copy into project |
| &nbsp;&nbsp;&nbsp;&nbsp;`instantiate_library_type` | | Instantiate versioned type |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_to_multi_instance_fb` | | Add type as multi-instance member |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_library_folder` | | Create folder in library |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_library_folder` | | Delete folder (recursive option) |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_type_xml` | | Export type version to XML file |
| &nbsp;&nbsp;&nbsp;&nbsp;`open_global_library` | | Open .al19 file |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `openMode` | | ReadOnly (default) / ReadWrite |
| &nbsp;&nbsp;&nbsp;&nbsp;`close_global_library` | | Close global library |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_global_library` | | Create new .al19 |
| &nbsp;&nbsp;&nbsp;&nbsp;`save_global_library` | | Save global library |
| &nbsp;&nbsp;&nbsp;&nbsp;`archive_global_library` | | Archive to .zal19 |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_check` | | Preview what update_project would change |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_project` | | Apply library updates to project (destructive) |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `deleteUnusedVersions` | | Auto-delete versions with no instances |
| &nbsp;&nbsp;&nbsp;&nbsp;`promote_to_global` | | Copy types to global library |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `forceUpdateMode` | | SetOnlyHigher / ForceSetAny / NoDefaultVersionChange |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `structureConflictMode` | | CancelIfConflicts / UpdateStructure / RetainStructure |
| &nbsp;&nbsp;&nbsp;&nbsp;`compare_to_target` | | Diff two libraries |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_unused_types` | | Remove types with no instances (dry-run + confirm) |
| **`xref`** | **4** | **Cross-reference analysis** |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_references` | | Cross-refs for one block (who it calls, what it reads/writes) |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `filter` | | all / with_refs / without_refs / unused |
| &nbsp;&nbsp;&nbsp;&nbsp;`find_unused` | | All blocks not called from any OB chain |
| &nbsp;&nbsp;&nbsp;&nbsp;`find_callers` | | Which blocks call/reference a target block |
| &nbsp;&nbsp;&nbsp;&nbsp;`find_orphaned_instance_dbs` | | Instance DBs whose owning FB is missing |
| **`alarm_text`** | **15** | **PLC alarm text lists, entries, alarm classes** |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_textlists` | | List system + user text lists |
| &nbsp;&nbsp;&nbsp;&nbsp;`create_textlist` | | Create text list (optionally with entries) |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `listRange` | | Decimal (default) / Binary / Bit |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_entries` | | Read entries for a text list |
| &nbsp;&nbsp;&nbsp;&nbsp;`add_entries` | | Add entries to text list |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_entries` | | Update entries by From value |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_entries` | | Delete entries by From value |
| &nbsp;&nbsp;&nbsp;&nbsp;`update_textlist_comment` | | Update comment (direct API) |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_textlist` | | Delete user text list |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_alarm_texts` | | Export alarm instance texts to XLSX |
| &nbsp;&nbsp;&nbsp;&nbsp;`import_alarm_texts` | | Import alarm texts from XLSX |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_textlists` | | Export text lists to XLSX |
| &nbsp;&nbsp;&nbsp;&nbsp;`import_textlists` | | Import text lists from XLSX |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_alarm_classes` | | Export alarm class definitions |
| &nbsp;&nbsp;&nbsp;&nbsp;`import_alarm_classes` | | Import alarm class definitions |
| &nbsp;&nbsp;&nbsp;&nbsp;`export_alarm_data` | | Export alarm data to CSV or XLSX (scope: texts/lists/classes) |
| **`admin`** | **11** | **Server administration, usage analytics, export storage** |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_stats` | | Call statistics per tool+action (counts, errors, avg duration) |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_recent_errors` | | Last N failed calls with device/firmware context |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_device_profiles` | | All unique devices seen (order number, firmware, TIA version) |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_version` | | Server version, DB path, DB size |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_system_info` | | OS, .NET, TIA Portal version, uptime, memory |
| &nbsp;&nbsp;&nbsp;&nbsp;`list_exports` | | Recent exports stored in DB (with size, format, timestamp) |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_export` | | Retrieve export content with paging |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `raw` | | Content only, no metadata header |
| &nbsp;&nbsp;&nbsp;&nbsp;`delete_export` | | Delete single export |
| &nbsp;&nbsp;&nbsp;&nbsp;`clear_exports` | | Delete expired exports |
| &nbsp;&nbsp;&nbsp;&nbsp;`save_export` | | Save export to file (auto-decodes base64 for XLSX) |
| &nbsp;&nbsp;&nbsp;&nbsp;`open_file` | | Open exported file with default Windows application |
| **`diagnostics`** | **6** | **Runtime PLC state, network scan, connection config, compare** |
| &nbsp;&nbsp;&nbsp;&nbsp;`scan_devices` | | Network scan — discover accessible PLCs |
| &nbsp;&nbsp;&nbsp;&nbsp;`configure_connection` | | Set up download connection programmatically |
| &nbsp;&nbsp;&nbsp;&nbsp;`get_plc_status` | | Online/offline state |
| &nbsp;&nbsp;&nbsp;&nbsp;`go_online` | | Connect to PLC |
| &nbsp;&nbsp;&nbsp;&nbsp;`go_offline` | | Disconnect from PLC |
| &nbsp;&nbsp;&nbsp;&nbsp;`compare_online_offline` | | Compare project vs PLC — all blocks, tags, UDTs |
| **`download_upload`** | **4** | **Download to PLC, upload station into project** |
| &nbsp;&nbsp;&nbsp;&nbsp;`download_check` | | Pre-flight: connection, compile status |
| &nbsp;&nbsp;&nbsp;&nbsp;`download_to_device` | | Download to PLC (requires confirm) |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `mode` | | software_changes (default) / software / hardware_software / hardware |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳ `stopModules` | | Stop PLC before download (default: false) |
| &nbsp;&nbsp;&nbsp;&nbsp;`upload_check` | | Enumerate upload connection options |
| &nbsp;&nbsp;&nbsp;&nbsp;`upload_station` | | Upload PLC station into project (requires confirm) |

**Total: 16 tools (14 meta-tools + 2 standalone), 166 actions**

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

This list will grow as we collect telemetry data from users and expand testing across more device families, firmware versions, and TIA Portal editions.

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

- **HMI integration** — Openness API access to HMI screens, tags, and alarm views
- **Runtime monitoring** — live PLC data via Web API and OPC UA (S7-1200 V4.4+, S7-1500)
- **TIA Portal V20 support** — updated Openness DLLs and new API features
- **PLCSIM Advanced** — simulation control for offline development and testing
- **S7-1500 expanded testing** — broader device family coverage with telemetry-driven validation

Have a feature idea? [Submit it on GitHub](https://github.com/a4webdev/tiacommander-mcp/issues).

---

## Quick Start

1. **[Download](https://github.com/a4webdev/tiacommander-mcp/releases/latest)** the latest portable `.zip` from GitHub Releases
2. Extract and follow the **[Quick Start Guide](docs/1-QUICKSTART.md)** — everything you need to get up and running in 5 minutes: extract, Openness group setup, AI client configuration for all 7 tested clients, license activation, and first commands.

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
