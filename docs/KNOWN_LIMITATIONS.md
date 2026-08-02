# Known Limitations

Confirmed limitations of the Siemens TIA Portal Openness V19 API that affect
TiaCommander functionality. These are Openness API constraints, not TiaCommander bugs.

---

## TIA Portal version compatibility

TiaCommander is built against the Openness API level V19.

- **TIA Portal V19: fully supported.** This is the tested and recommended version.
- **TIA Portal V17 / V18: verified working** (tested: attach, open, block
  listing, logic reads, compile), but are not the primary supported level -
  verify your own workflow.
- **TIA Portal V16 and older: NOT supported for project work.** These API
  levels lack object model classes this build depends on, so project
  operations (open, create, and everything after them) cannot succeed.
  TiaCommander detects this at startup and refuses project operations with a
  clear message instead of failing mid-operation. To work with TiaCommander,
  use TIA Portal V17 or newer with the Openness option installed.
- On machines with several TIA Portal versions installed, `get_info` lists
  every installed Portal/API combination and `session configure
  preferredTiaBinding` selects which one TiaCommander binds to (takes effect
  at the next start). A project saved by a newer TIA Portal version can never
  be opened by an older one - that is a TIA Portal rule independent of
  TiaCommander.

---

## Network scan requires an open project

Scanning for accessible devices on the network requires a project to be open
in TIA Portal. The scan cannot be performed with TIA Portal running but no
project loaded. Open any project before using `scan_devices`.

## Direct IP address and PROFINET device name assignment

The Openness V19 API does not support direct DCP-based IP address or PROFINET
device name assignment to devices on the network (equivalent to TIA Portal's
"Online access → Online & Diagnostics → Functions → Assign IP address").

To change a device's IP address or PROFINET name, configure the values in
the project's device properties and download to the PLC. TiaCommander supports
this through the project-side configuration workflow.

## Network scan returns limited device information

The network scan returns only basic device information: IP address, device
series (e.g. "S7-1200"), MAC address, and PROFINET device name. Detailed
information such as order number, firmware version, or hardware version is
not available from scanned devices.


## Adding a device to the project is required before changing network configuration

The network scan returns only basic device information (IP, device series, MAC,
name) — not enough detail to automatically add the matching device to a project.
Before using `set_network_config`, ensure the target device exists in the project:

- Add the device manually in TIA Portal (Add new device → select from catalog), or
- Use `download_upload → upload_station` to upload the device configuration from
  a supported PLC into the project.

Once the device is in the project, use `hardware → set_network_config` to change
its IP address, subnet mask, gateway, or other network settings, then download
to the PLC to apply.

## First-time PG/PC connection must be configured in TIA Portal

Before TiaCommander can download to a PLC for the first time, the PG/PC
connection must be established once in TIA Portal:

1. In TIA Portal, go to Online menu → Accessible devices
2. Select the PG/PC interface (network adapter)
3. Click Update list to scan for devices
4. Select the target device by its MAC address
5. Click Show

After this one-time setup, TiaCommander can handle all subsequent downloads
programmatically via `configure_connection` and `download_to_device`.


## TIA Portal project tree does not auto-refresh after network changes

After changing the PROFINET device name and downloading the hardware
configuration to the PLC, the TIA Portal project tree does not update
automatically. To see the updated name in TIA Portal, click "Update
accessible devices" in the Online access section of the project tree.
TiaCommander's `scan_devices` can also be used to verify the change.

## GitHub Copilot requires Agent Mode to use MCP tools

GitHub Copilot in VS Code and Visual Studio only uses MCP tools when set to
**Agent Mode**. In the default "Ask" or "Edit" modes, Copilot ignores MCP
servers entirely — TiaCommander tools will not appear or be called.

To ensure Copilot uses TiaCommander:

1. Open Copilot Chat
2. Select **Agent** from the mode dropdown in the chat input area
3. Prefix your prompt with **MCP** (e.g. "MCP connect to TIA Portal and list devices")

The "MCP" prefix signals Copilot to prioritize MCP server tools for the request.
Without it, Copilot may attempt to answer from general knowledge instead of
calling TiaCommander.

## Manager window may open in the system tray instead of the foreground

When the AI client calls `open_manager`, the Manager window may not appear
on screen immediately. On some Windows configurations, the window opens
minimized to the system tray (near the clock) instead of in the foreground.
Click the TiaCommander icon in the tray to bring it up. This behavior varies
between Windows versions and is not a TiaCommander bug.


## Online PLC diagnostics data cannot be read programmatically

The actual firmware version, serial number, hardware version, and article number
of a connected PLC cannot be read through TiaCommander. This information is only
available in TIA Portal's **Online & Diagnostics > General** view.

When a download fails because the project device configuration doesn't match the
physical PLC (e.g. project says firmware V4.6 but the PLC has V4.5), TiaCommander
reports the error but cannot tell you what firmware the PLC is actually running.

**What to do:** In TIA Portal, go online to the device, then open **Online &
Diagnostics > General** to see the PLC's actual firmware version, hardware version,
and serial number. Adjust the project device configuration to match before
downloading.

This is a limitation of the Siemens Openness V19 API — the online diagnostics
data reading capability is not exposed through the public API.

## Project-tree folders: rename and moving items

TiaCommander can create, list, and delete folders (groups) in the PLC project
tree across Program blocks, Technology objects, External source files, PLC tags,
PLC data types, and Watch and force tables.

Two operations are not possible, because the TIA Portal Openness API does not
expose them:

- **Renaming a folder.** The folder name is read-only through the API. Rename
  folders in the TIA Portal user interface instead.
- **Moving items between folders in one step.** There is no direct move
  operation in the API. Move items in the TIA Portal user interface, or create
  new items directly in the target folder. (An export-and-reimport based move
  may be added to TiaCommander in a future version.)

Deleting a non-empty folder permanently removes everything inside it; the
folders tool therefore requires an explicit flag and confirmation phrase first.

## Tag tables cannot be copied

PLC tag names are unique across the whole controller, not per table, so a
table copy would collide on every tag name. This is a TIA Portal data-model
rule, not a tool restriction. What you can do instead: move a table between
folders with the tag move_table action (contents are preserved exactly),
or create a new table and add the tags you need under new names.

## New data types (UDTs) are created at the root of PLC data types

The engineering API cannot create a UDT directly inside a folder. Create
the UDT first, then move it into its folder with the udt move action.
Member edits on a foldered UDT keep it in its folder automatically.

## PLC diagnostics cannot be read programmatically

The engineering API is design-time only. It cannot read the CPU diagnostic
buffer, the event log, per-module fault or LED status, PROFINET device
diagnostic state, or even whether the PLC is in RUN or STOP. This was
verified directly against a live PLC. The only live signal available is
whether an online connection is currently established.

What is available: the configured firmware version and order number of each
module (a hardware inventory), read from the project.

For live diagnostics, use TIA Portal's Online & Diagnostics view or the
CPU's built-in web server. Runtime data access over a dedicated channel
(such as OPC UA) is tracked as a separate roadmap item.

For live runtime values, use the `live_data` tool, which reads over a dedicated
protocol rather than the engineering API.

On-request diagnostics (semi-manual, being retired): TiaCommander can deploy a
GET_DIAG collector block plus a retentive data block, and after two manual
clicks in TIA Portal (create a snapshot, then copy snapshots to start values)
read the captured values back with `snapshot_read`. The deployment step is no
longer offered on the tool surface: it advertised ten diagnostic families but
could only ever produce one, the two clicks cannot be automated, and what comes
back is the value at the moment of those clicks rather than live data.
`snapshot_read` remains available as a legacy path.

## CPU operating mode (RUN/STOP) is not available from the engineering API

`get_plc_status` reports the CONNECTION state — connected, not reachable,
protected, and so on. It does not report whether the CPU is in RUN or STOP,
and no tool can report it from the engineering API: the operating mode is not
exposed there at all. This is a property of the API, not a gap in TiaCommander.

To establish RUN/STOP, use `live_data` with `action=read_diagnostics` and
`kind=buffer`. The CPU's diagnostic buffer records mode transitions with
timestamps, for example "CPU changes from STARTUP to RUN mode". Note this shows
the last logged transition rather than a live mode register. For a definitive
live reading, use TIA Portal's Online & Diagnostics view or the CPU's
integrated web server.

## Technology objects and external source files: folder browsing only

The folder tree for technology objects and external source files can be
browsed, created, and searched like every other container. However, the
listings do not include per-item detail (such as a technology object's
kind or an external source file's language): the engineering API does not
expose reliable attributes for these, and no dedicated read/write actions
exist for their items yet. Item counts and folder structure are complete;
per-item type filtering is limited to "all" for these two containers.

## User data type listings do not show member counts

Tree and search listings show each user data type's name and number, but
not how many members it contains: the engineering API does not expose a
type's members without a full export. Use the UDT structure action to
inspect a specific type's members.

## The activity log covers TiaCommander's actions only

`admin get_activity_log` shows every tool call TiaCommander made — when, which
tool and action, against which device, how long it took, and whether it
succeeded.

It does **not** show work you do by hand in TIA Portal. Opening a block, editing
a network, compiling or downloading from the Portal window leaves no entry,
because TiaCommander was not involved and TIA Portal does not make that history
available to any external interface.

**What this means in practice:** a project can change between two logged calls
with nothing in the log to explain it. The activity log is a complete record of
what the AI assistant did, not a complete record of what happened to the
project.
