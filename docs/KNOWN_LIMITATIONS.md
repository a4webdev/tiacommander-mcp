# Known Limitations

Confirmed limitations of the Siemens TIA Portal Openness V19 API that affect
TiaCommander functionality. These are Openness API constraints, not TiaCommander bugs.

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
