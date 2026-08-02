# Changelog

All notable changes to TiaCommander are documented here.

## v3.15.2 (02-08-2026)
- The published tool descriptions no longer carry internal research references. The text an AI client reads is tidied up; no tool, action or argument changed — descriptions only.
- Release packaging and third-party licence review: complete attribution (THIRD-PARTY-NOTICES.md and a licenses/ folder with the full text of every third-party licence) and a verified, minimal file set in the download.

## v3.15.1 (02-08-2026)
- The watch tool now documents how addresses are matched: delete_entry and update_entry accept either the bare address or the exact symbolic name, the answer says which one matched, and an address owned by more than one tag is refused rather than guessed.
- Removed a line that told you to use the quoted symbolic form for every operation after adding an entry. That is no longer necessary -- it was the limitation fixed in v3.15.0.
- folders find now states that it matches item names, never folder names. Use get_tree to find a folder.
- Corrected the delete_entry parameter list, which named a table parameter the action does not accept.

## v3.15.0 (02-08-2026)
- Fixed: deleting or updating a watch or force table entry by its plain address (for example %M60.1) failed with "EntryNotFound" whenever a PLC tag owned that address -- which is exactly the case on tables built from tags. TIA stores those entries under the tag name, and only the tag name was being matched.
- Both the single and the bulk form are fixed, on watch tables and force tables alike. A bulk delete that mixes tag-owned and plain addresses now succeeds for all of them instead of skipping the tagged ones.
- When an address is matched through its tag, the result says so: matched symbolically as "TagName". You can always tell which entry was acted on.
- If a plain address is owned by more than one tag, the call is refused and names the candidates, and nothing is changed. Re-issue it with the exact tag name.
- Addresses that genuinely do not exist still report EntryNotFound, naming the address.
- No change to entries at plain addresses with no tag -- those already worked and behave exactly as before.

## v3.14.2 (01-08-2026)
- Two arguments that already worked are now declared in the tool schema, so an AI client can discover them instead of having to know: family on blocks_write create_fb, and since on the admin activity log.
- Removed an unused argument name from an internal code path. No action was reachable through it.

## v3.14.1 (01-08-2026)
- Every tool now checks argument names. All 205 actions across all 18 tools carry a per-action list of the arguments they accept.
- The published tool list (data/tool-list/tools.json) now states, per action, exactly which arguments that action takes. Previously this existed only in the description text.
- Fixed: the tool-list generator had stopped working after an internal refactor, so the published README and tool list had been frozen since 29-07-2026. They now describe the current server again -- including the live RUN/STOP read added in v3.13.1 and the data-block folder options added in v3.13.2.

## v3.14.0 (01-08-2026)
- New: passing an argument a tool does not recognise no longer fails silently. The tool now adds a short note naming the argument it ignored and listing the ones that action accepts, then carries on and answers normally.
- The note never blocks a call, and it appears on failed calls too, so a mistyped argument can no longer produce a plausible-but-wrong answer with no signal.
- Fixed: live_data listed two arguments as valid that it never actually used -- tableName on read_watch_table and deviceName on connect. Passing either did nothing and said nothing. They are now reported like any other unrecognised argument.

## v3.13.2 (01-08-2026)
- Fixed: creating a data block with memoryLayout=Standard, or into a folder with targetFolder, failed on S7-1200 V3.0.2 CPUs. Both now work. They were already working on V4.6.
- Cause: the block template carried an OPC-UA attribute that older CPUs do not implement, and TIA refused the import. The attribute is now left out and TIA supplies its own value.
- No change on newer CPUs: TIA fills in the same value the template used to hardcode, so OPC UA accessibility of created blocks is unchanged.
- Note: plain db create was never affected - it uses a different path that does not touch the template.

## v3.13.1 (01-08-2026)
- New: get_plc_status now reports the CPU's ACTUAL operating state, read live from the PLC, instead of the last mode change recorded in its diagnostic buffer. Proven on both firmware generations (V3.0.2 plaintext and V4.6 TLS).
- New: live_data action=read_diagnostics accepts kind=opstate for the same live reading. It is deliberately not part of kind=all, so existing calls do not gain a PLC round-trip they did not ask for.
- Improved: the live read is bounded to 3 seconds. If it times out, errors, or returns a value this firmware support does not model, the output falls back to the last logged mode change AND says which of those three happened, labelled "last known". A fallback is never presented as a live reading.
- Fixed: a state that could not be read now renders UNKNOWN, never STOP. About 4% of these reads fail, and failures cluster around mode changes, so the moment a false STOP was most likely was also the moment it misled worst.
- Fixed: a read that exceeds its budget no longer leaves the connection able to corrupt the next call. The session is discarded rather than reused.
- Note: there is still no subscription and no push. A live subscription was measured to be incompatible with ordinary reads on the same connection, so it is not offered. See agents/research/findings/223-subpump-spike.md.

## v3.13.0 (31-07-2026)
- New: data blocks can now be created directly inside a folder. db action=create takes targetFolder ('Motors/Drives' syntax) and createParents, matching how blocks, tag tables and watch tables already work. The server always accepted these; they were simply never declared, so a client that validates arguments could not send them.
- Improved: get_plc_status now reports the CPU's last known operating mode, not just the connection state. It resolves the IP of the device you named, opens a live-data session if one is free, confirms the CPU's identity against the project, and reports the last logged mode change with its timestamp.
- Note: that is the last recorded mode change, not a live reading. A CPU that stopped without writing to its diagnostic buffer will not be reflected. The operating mode is not available from the engineering API at all, so this is the honest best available.
- Note: live data serves one PLC at a time for the whole machine. If get_plc_status opens a session it says so and leaves it open for you to reuse; call live_data action=disconnect to release it. It never takes over a session already in use by another client, and it will not open one at all when more than one AI client is attached -- it tells you the command to run instead.
- Note: if the named device has several configured IP addresses and none is on a subnet, get_plc_status refuses rather than guessing which one is the CPU. Reading the wrong PLC returns values that look right and are not.

## v3.12.0 (31-07-2026)
- New: data blocks can now be created with a standard memory layout. db action=create takes memoryLayout, either 'Optimized' (the default, unchanged) or 'Standard'. Use Standard when the block must be readable on an older CPU -- optimized blocks have no fixed byte addresses and cannot be read there at all. An unrecognised value is rejected rather than quietly falling back.
- Improved: get_plc_status now says plainly that it reports the CONNECTION state and not the CPU's RUN/STOP mode. The operating mode is not available from the engineering API at all, so it points you to live_data action=read_diagnostics kind=buffer, which shows the CPU's logged mode changes. Note that is the last recorded change rather than a live reading.
- Improved: scan_devices now labels every discovered device as either in the open project or not, and its closing advice no longer treats every result as a download target. Network discovery is a broadcast and reaches equipment on other subnets, including other people's machines, so a discovered address is not proof of which device it is.
- Note: scan_devices still lists everything it finds. Nothing is hidden or filtered -- a discovery tool that hides devices would give you false confidence about your network.
- Internal: all tool definitions are now built through a single code path, verified against a snapshot of the previous tool list to prove no tool's inputs, description or ordering changed.

## v3.11.0 (29-07-2026)
- New: read the CPU diagnostic buffer with live_data action=read_diagnostics kind=buffer. This is the same event log TIA Portal shows under Online and Diagnostics: newest first, timestamped from the CPU clock in UTC. Optional maxEntries (default 50).
- Note: event text is resolved locally, so an event id outside the catalog is reported as unresolved rather than shown blank.

## v3.10.0 (29-07-2026)
- Improved: live data messages now describe what happened to your PLC connection in plain language, instead of exposing internal component names and version numbers.
- New: TiaCommander now ships THIRD-PARTY-NOTICES.md and a licenses/ folder listing every third-party component it includes, with the full text of each licence.
- Changed: the admin activity log (a timeline of every internal call the server made) is no longer available by default. It was a diagnostic surface rather than a user feature.
- Note: no functionality was removed from any PLC-facing tool. Live reads, watch tables, tag tables and diagnostics are unchanged.

## v3.9.0 (29-07-2026)
- New: read a PLC tag table's live values from the running PLC. live_data action=read_tag_table takes a device and a tag table name; tags can be named symbolically or by absolute address (%M1.0, %MB1, %MW20, %MD4, and the %I/%Q equivalents).
- New: live_data action=list_readable shows what the PLC actually exposes for reading, grouped by data block and memory area. Use it when a tag does not resolve -- it distinguishes "not declared" from "this whole area is empty".
- Improved: live_data action=read_watch_table now also accepts a project watch table by name, not just a list of symbols.
- These reads are polling, not streaming. Each call returns the values as of that call; there is no subscription. Reads only -- this surface never writes, modifies or forces.
- Safety: reading a tag table now checks the named device against the PLC you are connected to, and refuses if they are different hardware rather than returning plausible but wrong values.
- Fixed: reading a watch table could crash if the PLC exposed two symbols with the same name.
- Fixed: the live-data helper component is now built and deployed automatically, so it can no longer fall behind the rest of the product.

## v3.8.0 (29-07-2026)
- Removed 77 legacy tool names left over from the move to the grouped meta-tools. They were never offered to AI clients and, across 4,577 recorded calls over two months, were used exactly once -- by an internal test script, unsuccessfully. Every one of them has a supported equivalent on a meta-tool.
- Removed six redirect stubs for actions that moved or were withdrawn in v3.0.0. Calling one now reports an unknown action instead of a redirect message.
- Removed an undocumented second name for opening a project. Use session action=open, which is unchanged and also starts TIA Portal if it is not running.

## v3.7.2 (29-07-2026)
- Tag tables can now be exported as raw TIA XML (format=xml), alongside CSV and XLSX.
- The in-PLC diagnostics snapshot reader is no longer offered. It reads a data block that only the already-retired setup step could create, so it could not work for a new installation. The code remains in place.
- Nineteen refusal messages now begin with "Error:" like the rest, so a refused call is recorded as a failure rather than a success in the log and activity history.

## v3.7.1 (29-07-2026)
- Faster hardware reads. Reading a device's configuration asked TIA Portal for a fixed list of nine settings on every module, including ones that module does not have, and absorbed the resulting errors. It now asks once which settings a module actually has. Reading a device is roughly 2.5x faster (965 ms to about 350 ms on the test station) and the output is byte-for-byte the same.

## v3.7.0 (29-07-2026)
- Failed tool calls are now recorded as failures in the log. Previously a tool that reported a problem by returning a message -- rather than by raising an error -- was written to the log as "completed", so a failed call and a successful one looked identical. The log line now names the tool, the reason and the elapsed time.
- Rejected calls say which stage rejected them: an unrecognised action or an unknown tool name is now logged distinctly from a failure inside a tool.
- Errors coming back from the TIA Portal API now carry the error type name, not just its text, so the source of a failure can be identified without guesswork.
- New optional diagnostic mode: with detailed logging enabled, every internal error is recorded even when the program recovers from it silently. Repeated identical errors are collapsed so the log stays readable. Off by default; normal log volume is unchanged.

## v3.6.0 (28-07-2026)
- Retired: the in-PLC diagnostics deployment step (diagnostics setup_diag_read) is no longer offered. It advertised ten diagnostic families but could only ever produce one -- three had no template support at all, four more were never given the hardware ID they needed, and the read-back required two manual clicks in TIA Portal that no interface can automate. What came back was the value at the moment of those clicks, not live data. For live values, use live_data.
- Still available: diagnostics snapshot_read, which reads back previously captured values. It stays because it is currently the only way to see a CPU operating state.
- Nothing was deleted. The collector blocks and templates still ship, and existing projects that already have the kit deployed keep working.
- Docs corrected: the tool tables in both READMEs understated the surface. They now read 18 tools and 209 actions (previously 16 tools, and three different action counts across the two files), and the diagnostics section lists its actual actions.

## v3.5.0 (28-07-2026)
- New: admin get_activity_log -- a timeline of every tool call the server made, newest first, with time, tool, action, device, duration, success and error. Optional "since" and "limit". It records only what TiaCommander did; actions you perform by hand in TIA Portal are not visible to any interface and do not appear.
- Live PLC connections are now diagnosable. The S7 bridge previously failed in silence: nothing it did was written anywhere. It now reports when it starts, what was asked and what came back, how long the round trip took, and -- when it stops -- whether it exited on its own or crashed. A failure that used to read as an unexplained timeout now says which step failed.
- Fixed: a long-standing condition that could make the bridge freeze if it produced a burst of output. Its error channel was being filled but never emptied.
- Fixed: log lines could be silently lost when several AI clients ran at once, because every process wrote to one shared file and the loser of a collision was discarded. Each process now writes its own daily log. Existing cleanup and the 14-day retention are unchanged.
- Diagnostic logging is more complete when enabled: full message frames instead of the first 200 characters, plus tool arguments, results and timings. Off by default; nothing changes in normal operation.
- Security: passwords stay masked everywhere, now including the stored diagnostics database. Error details recorded there were previously written without masking, and are redacted both when written and when displayed.
- The bridge's own log is now timestamped, so it can be lined up against the main log.

## v3.4.0 (27-07-2026)
- Safety for shared sessions: while more than one AI client is attached, destructive actions (closing the project, saving, save-as, and any delete) now require a confirmation phrase, so one client cannot quietly discard work another one is in the middle of. With a single client nothing changes.
- get_info and session get_state now report how many AI clients are attached, whether this instance is the one in charge, and the confirmation requirement.
- New optional setting: allow direct HTTP clients on the local listener. Off by default, and it does not affect several AI clients working together -- that is automatic either way. Available from the Manager (Paths tab) or via session configure httpEnabled.
- Progress messages (such as the TIA Portal permission prompt) now reach the client that made the call, while the call is still running, and are not sent to the other clients.

## v3.3.0 (27-07-2026)
- SEVERAL AI CLIENTS AT ONCE. TiaCommander now serves more than one MCP client on the same machine -- Claude Desktop and one or more Claude Code sessions, Codex, Cursor and so on, all at the same time. Previously the second client to start received no tools and no error message at all.
- Nothing to configure. Every client keeps its existing setup; the first one to start takes charge of TIA Portal and the rest are routed through it automatically.
- If the client that is in charge goes away, another one takes over within seconds and the remaining clients reconnect on their own. A call that was running at that moment returns a clear error instead of hanging.
- Tool calls are still executed strictly one at a time, because TIA Portal is a single program with a single open project. Several clients means several conversations, not faster work.
- An instance that genuinely cannot serve now exits with a specific status code and records which instance holds the connection, so the situation can be diagnosed.
- Log entries now record which process wrote them.

## v3.2.0 (27-07-2026)
- Continued groundwork for running several AI clients at once (second phase; still not user-visible). Tool calls now run on a single dedicated worker so that when multiple clients are supported, they cannot collide inside TIA Portal. Behaviour for the current single client is unchanged.
- A tool call that cannot get the shared TIA session within 10 seconds now comes back with a clear "busy, nothing changed, retry" message instead of waiting indefinitely, and that call is guaranteed not to run later behind your back.
- Requests that arrive after a client disconnects are discarded rather than executed against a project nobody is watching.
- Fixed: log lines could be silently lost when more than one part of the program logged at the same moment.

## v3.1.0 (27-07-2026)
- Groundwork for running several AI clients against TiaCommander at once (first phase; not yet user-visible). Internal transport spike proven and the response path refactored behind a per-client output abstraction, with no change to how the current single client is served -- verified byte-for-byte identical.
- A second server instance that cannot serve now exits with a distinct, non-zero status and records which instance is holding the connection, instead of exiting silently with no message. This makes the "second client sees no tools and no error" situation diagnosable.

## v3.0.1 (27-07-2026)
- The live data tool's display name is now simply "Live PLC values + diagnostics".

## v3.0.0 (27-07-2026)
BREAKING CHANGES - one uniform container model everywhere:
- Removed: the folders "list_folders" action and the library "list_folder" action. Use get_tree with depth=1 and includeItems=true for the old immediate-contents view; it shows the same folders, items, version state and counts, and the paths it prints feed directly into create/rename/delete. Calls to the removed actions return a clear error pointing at the replacement -- never a wrong result.
- Removed: the library "maxDepth" and "section" arguments. Use "depth" (0 = unlimited, 1 = immediate children -- the same rule as the project-tree folders tool) and "typeFilter" (all, types, master_copies). Passing the old names returns a clear error naming the new argument.
- Changed: library get_tree defaults now match the folders tool -- path is optional (defaults to the root), nested defaults to true (indented view), includeItems defaults to false (folder structure only). depth=1 now means exactly "immediate children" (previously one extra folder level leaked through).
- Added: an optional "limit" argument on tree and search listings (folders get_tree/find, library get_tree/find), applied below the existing output ceilings. All truncation is announced with one uniform, clearly visible NOTICE line.

## v2.65.0 (27-07-2026)
- Watch table, tag table, UDT and data block listings now show each item's full folder path (Folder/Sub/Name) and use the same uniform item format as the tree and search listings, with clean totals footers. Tables inside folders are no longer shown as bare names.
- Fixed: the UDT list ignored folders entirely; foldered UDTs now show their location.
- Totals across the tree view and the per-type listings now reconcile (same counts from both views).

## v2.64.0 (27-07-2026)
- Project-tree and library listings now use one uniform item format everywhere: Name [key=value, ...]. Blocks show type, number, language, owning FB (instance DBs) and memory layout; tag tables show their tag count; watch and force tables show entry counts and a force flag; library types show version count, latest version, state and default version; master copies show what kind of object they wrap.
- The typeFilter argument is now container-aware: watch and force tables can be filtered with typeFilter=watch or force, and the library accepts typeFilter=types or master_copies. A filter value that does not apply to a container is now rejected with the list of valid values instead of silently returning an empty result.
- Fixed: the "wrapped object" line in master copy details never appeared; it now correctly shows the wrapped object's kind.

## v2.63.0 (27-07-2026)
- Internal groundwork for uniform project-tree and library browsing: tree and search listings now render from a shared read-only snapshot. No visible changes -- output is identical to the previous version. Tree listings on deeply nested projects are slightly faster.

## v2.62.0 (27-07-2026)
- live_data can now remember a PLC's password. New set_credential stores it encrypted on this machine (a copied database is useless elsewhere); connect then uses it automatically when you don't pass a password. list_credentials shows what is stored (never the password itself) and delete_credential removes it.
- The stored password is encrypted at rest and is never shown or written to logs.

## v2.61.2 (27-07-2026)
- live_data now warns when a call includes an unrecognized argument name (e.g. a typo like "hostname" instead of "host") instead of silently ignoring it, so a mistyped argument no longer produces a misleading result. The call still runs -- the warning is added to the top of the response.
- live_data diagnostics: the note about data not available over S7CommPlus is now shown only for a full diagnostics read, not when you ask for a specific kind (such as identity).

## v2.61.1 (27-07-2026)
- Security fix: sensitive arguments (passwords) were written in cleartext to the local diagnostic log because every raw request was logged at debug level. Secrets are now masked in all log output, and debug logging is off by default (set TIACOMMANDER_LOG_DEBUG=1 to re-enable for troubleshooting).
- Logs now self-prune after 14 days.
- live_data: a dropped PLC connection now reports a clear "reconnect" message instead of a generic "not connected".
- The live-data helper no longer writes TLS session keys to disk unless explicitly enabled for debugging.

## v2.61.0 (27-07-2026)
- New live_data tool: read live PLC values and diagnostics over a secure S7CommPlus (TLS) connection -- data the engineering API cannot provide. Requires S7-1200 firmware V4.5+ or S7-1500 V2.9+.
- Reads (v1): live data-block values (whole block or selected members; optimized and standard blocks, symbolic, with quality codes), live values for a list of symbols, and diagnostics -- the CPU order number and firmware version read live from the PLC, the protection level, and active plus configured alarms.
- The connection uses a separate helper process; if it is not present, live_data reports a clear degraded-mode message instead of failing. Reads only in this version (no writes).
- Note: the CPU diagnostic buffer, per-module fault states and activity trace are not available over this path yet.

## v2.60.0 (26-07-2026)
- TIA Portal version selector: on a PC with several TIA Portals installed, get_info and the Manager dropdown list each installed version with its path, and session configure preferredTiaBinding=<version> (e.g. V19, V18, V16) picks which one to run. TiaCommander works with V16 through V20.
- Set the version before connecting and it applies immediately (no restart). Change it after you have connected and TiaCommander asks you to restart to apply it - a single process can only run one TIA Portal version at a time.
- Simpler and non-blocking: the earlier version-compatibility gating has been removed.

## v2.59.3 (26-07-2026)
- Clearer multi-version behavior: the Manager and get_info now list the TIA Portals INSTALLED on the PC (e.g. V19, V16), not every internal API folder. TiaCommander is backward compatible to TIA Portal V17 - V17, V18, V19 and V20 are supported for project work.
- On a TIA Portal V16 (or older) binding, TiaCommander still starts and every tool responds with a clear "unsupported TIA version - use V17 or newer" message instead of failing; status, configuration and switching the binding stay available so you can point it at an installed V17+ Portal.

## v2.59.2 (26-07-2026)
- Improved: TiaCommander now verifies the loaded TIA Openness API against the exact set of API surfaces it needs (a capability probe) instead of trusting version numbers. Any installed version with a gap - present or future - is refused up front with the missing pieces named, never a mid-operation crash.
- Verified: version switching tested end-to-end on a dual-Portal machine - V19, V18 and V17 API bindings all ran the full project workflow (attach, open, block listing, logic reads, compile); V16 is detected and refused gracefully; switching back restores full function.

## v2.59.1 (26-07-2026)
- Fixed: on machines where TiaCommander binds a TIA Portal V16 or older Openness API, project operations no longer fail with a NullReferenceException (GitHub #8). TiaCommander now detects the incompatible API level at startup and refuses project operations with a clear explanation and remedies; status, configuration and the Manager stay available. TIA Portal V17+ is required for project work (see Known Limitations).

## v2.59.0 (26-07-2026)
- New: multi-version TIA Portal support. TiaCommander now enumerates EVERY installed TIA Portal / Openness API combination (all products, all API folders, future versions included automatically) and reports them in get_info with which one is loaded and which is preferred.
- New: choose the binding with session configure preferredTiaBinding=<id> or the Manager's new dropdown on the Paths tab. The choice takes effect at the next start; until you restart, every Openness operation refuses with a clear message so work can never silently continue on the wrong version. Saving, archiving and closing your project stay available.
- Safety: versions older than the build level are listed but marked -- verified on a dual-Portal machine: such a binding cannot operate on projects with this build (it fails at the first project access) and is useful for diagnostics only. Newer-than-build API levels are never chosen automatically.
- New: Open buttons next to each configured folder in the Manager's Paths tab -- one click to the folder in Explorer.
- Robustness: the preference survives updates, a stale or invalid preference falls back to the default with the reason shown, and a hand-edited preference file is picked up safely.

## v2.58.0 (26-07-2026)
- New: interface member editing on FB/FC/OB blocks -- blocks_write add_interface_member, update_interface_member (including rename), delete_interface_member, in any section (Input, Output, InOut, Static, Temp, Constant).
- Safety: every interface change computes its BLAST RADIUS first -- the callers whose call sites go out of date (including multi-instance uses) and the block's instance DBs -- plus a clear warning that the next download reinitializes the affected instance data, resetting actual values including retained members. If the blast radius is non-empty the call refuses with the full report; re-run with confirm=true to apply. Temp/Constant changes are compile-scope only and apply directly.
- A wrong member name lists the block's members per section so the next call lands.

## v2.57.0 (26-07-2026)
- New: db get_member -- read any data block member by dotted path at any depth, including deep multi-instance chains inside instance DBs (e.g. a station block's settings inside a polling FB's instance DB). Shows the type, start values (including per-array-element), comments, and nested members; a wrong path lists the members at that level with did-you-mean suggestions.
- New: db set_start_value -- set start values by dotted path at any depth, on global AND instance DBs. Array elements via [index]; several values in one call via values{}. Every path is checked before anything is written -- one bad path aborts the whole call untouched. Reports old -> new with the member's type.
- Improved: reading a data block right after changing its start values no longer fails with an inconsistency error -- the tool compiles and retries automatically.

## v2.56.0 (26-07-2026)
- New: rung-level editing of existing LAD networks. blocks_read get_network reads any LAD network as a structured JSON rung model -- the exact same format create_block accepts -- including contact chains, parallel branches, boxes, coils, FB/FC calls, and ENO-cascaded call chains (enFrom).
- New: blocks_write insert_rung, update_rung, delete_rung, and populate_network -- add, replace, or remove individual rungs in an existing network without rebuilding the block. Networks are re-emitted whole with clean ID allocation and validated before every import.
- New: create_block now accepts enFrom on a rung to chain it from the previous rung's ENO instead of the power rail; chained rungs share one network.
- Safety: a network is only editable when the rung model reproduces its logic EXACTLY (verified on every read). Networks containing content outside the model are refused with a detailed reason -- nothing is ever silently dropped or flattened. Deleting a rung that another rung chains from is refused; an optional expectTitle guard protects against network index shifts.
- Improved: dotted operand paths (DB members, nested instances) now emit correct nested symbol components everywhere, and call pins accept literal constants.

## v2.55.0 (26-07-2026)
- New: blocks_read get_element_pins -- the "look before you wire" card. For any call or instruction in a LAD/FBD network it lists every pin with its section, the data type it expects (read live from the called block's interface or the instruction's instance), and its current wiring: the connected operand with its actual resolved type, literal constants, or the exact unwired state. Called without an element selector it lists the network's elements with their IDs.
- New: pin wiring (update_network_element pinWires) is now type-checked BEFORE anything is modified. A wrong pin name returns the full list of valid pins with types; a misspelled operand is caught ("not found as a PLC tag, DB member, or #interface member"); a type mismatch names both sides ("pin mb_addr expects Int; ... is Real"). force=true overrides for deliberate implicit conversions.
- Fixed: retargeting a wired pin no longer fails with "reference defined but not used" -- the previous source is cleaned up automatically when nothing else uses it.
- Improved: assigning a tag to an unwired pin now builds correct symbol paths for DB members (nested components), and internal ID allocation is audited before every import.
- Note: TIA renumbers element IDs on reimport -- when making several edits to the same block, re-read the element list between edits.

## v2.54.1 (26-07-2026)
- Fixed: get_call_tree no longer attributes an instance DB to FC calls. Instance DBs are now matched to their true owner (the FB or instruction whose instance they are), so system instructions like MB_SERVER show their instance DB correctly and FC nodes never carry one.
- Improved: find_call_paths documents that block names resolve recursively across all folders (bare or folder-qualified names accepted).

## v2.54.0 (26-07-2026)
- New: LAD block requests are now fully validated before anything is sent to TIA Portal. Malformed create_block specs -- an unknown box type, a misspelled or wrong-case pin name, a missing required pin, or a wrong value shape -- fail immediately with the exact JSON path of the problem (for example networks[2].rungs[0].box.inputs.s) and the list of valid alternatives, instead of a raw TIA exception after a long import attempt.
- New: SR/RS flip-flops now support independently conditioned branches on both pins. The secondary pin (SR: r1, RS: s1) accepts either an operand as before, or a nested rung object with its own contacts and parallel branches -- so set and reset conditions can each have their own logic chain, matching what you would draw in the LAD editor.
- Fixed: instruction reference in the tool description -- CALC uses equation and lowercase in1..inN pins, and edge boxes (PBOX/NBOX) take their edge memory bit via bitOperand.

## v2.53.0 (26-07-2026)
- New: call-graph navigation on the xref tool. get_call_tree renders the forward call tree of your program (default: every OB as a root) to any depth -- each node shows the folder-qualified block name, block number, the calling network, and the instance DB used at that call site; system instructions (timers, MB_CLIENT, ...) appear as leaves. Repeated subtrees are collapsed and recursion is detected, so heavily shared library blocks stay readable.
- New: find_call_paths lists every call path from your OBs down to a given block (for example Main [OB1] -> ModbusPoll [FB2] -> mb_query [FB1]). Complements find_callers, which shows direct callers only.
- Both actions accept bare or folder-qualified block names and an optional maxDepth (default 10). Nested calls through multi-instance FBs are followed like any other call.

## v2.52.0 (26-07-2026)
- New: read CPU and PROFINET diagnostics from your PLC. Two new diagnostics actions work together. setup_diag_read deploys a small diagnostics collector into your project (a dedicated Diagnostics folder with a collector block, a retentive data block, and a cyclic OB that runs it every scan) and never touches your OB1. snapshot_read then reads the collected values back and decodes them (GET_DIAG status per hardware ID, device-state bitmaps, and every other collected value in raw form).
- Note: the read-back is semi-manual by design. After you download the program and let the PLC run, you do two clicks in the opened data-block editor -- "create snapshot of the monitored values", then "copy snapshots to start values" -- and then run snapshot_read. TIA Portal offers no programmatic way to do those two clicks.
- Included: the shipped collector covers GET_DIAG, DeviceStates, ModuleStates, RD_SINFO, LED, Get_IM_Data and Get_Name on the S7-1200. You can enable a subset with the families option and choose hardware IDs with hwIds (or let them be discovered automatically).
- Improved: when a hardware ID cannot be diagnosed by GET_DIAG, the result now explains it and suggests re-running with explicit hardware IDs to drop that channel.
- Fixed: the data-block structure view no longer shows duplicated, unlabeled rows for device-state fields.

## v2.51.1 (25-07-2026)
- Fixed (critical): tag comments passed to add_tag were silently discarded on every path. Comments are now written (single adds and bulk adds - bulk applies them all in one pass), and if a comment cannot be applied the action says so instead of reporting plain success.
- Improved: tag get_table_details now shows each tag's comment, so comment problems are visible without a CSV export.
- Fixed: adding, updating or deleting a UDT member reported a "disposed object" error even though the change was applied correctly. The operations now report their actual result.
- Improved: reading or exporting a UDT that was just created, imported or moved no longer returns a raw internal error - the message now explains that the UDT must be compiled first and names the compile action.
- Improved: the UDT structure view now shows the folder location and the plain UDT name, matching the tag and watch table views.
- Fixed: an error message referred to a non-existent action delete_udt_member; it now names the real action (udt delete_member).
- Improved: bulk watch/force entry deletes and updates now name the address that failed, even when an earlier item in the same batch emptied the table.

## v2.51.0 (25-07-2026)
- Fixed (critical): adding tags to a table that lives inside a folder reported success while writing nothing. All tag actions (add, delete, details, search, comments, access flags, exports, address assignment list) now find tables anywhere in the folder tree, and failures are always reported as errors -- in bulk mode too.
- Improved: watch table, force table and data type (UDT) actions are folder-aware as well: tables and UDTs are found in any folder, entry edits and member edits keep them in their folder, and rename collision checks cover the whole tree.
- Improved: table and UDT names may be given as a plain name (searched everywhere) or as a full path like 'Folder/Sub/Table'.
- Improved: "block not found" suggestions now also catch abbreviated names (e.g. a shortened middle part), and the error points to folders find as well as get_tree.
- Fixed: the folder tree footer counted leaf-empty folders twice ("3 empty, 5 in empty subtrees"); the two counts are now mutually exclusive and clearly labeled.
- Fixed: an error message referred to a non-existent action create_watch_table; it now names the real action (watch create_table).

## v2.50.0 (25-07-2026)
- New: tag move_table, watch move_table and udt move - move tag tables, watch/force tables and data types between folders (or back to the root with targetFolder=''). Contents are preserved exactly; on any failure the item is restored to its original location, and if even that fails the backup file path is reported - data is never lost silently.
- New: foldered UDTs via create-then-move: create the UDT, then move it into its folder.
- Note: tag tables cannot be copied - PLC tag names are unique across the whole controller, so a copy would always collide. The move action documents this.

## v2.49.0 (25-07-2026)
- New: folders action find - glob search (* and ?) across any project-tree container. Returns each match with its type, number, language and folder location. Use find when you know part of a name, get_tree when you need the map.
- New: connecting or opening a project now reports the project's shape in one line - devices, blocks, folders, tag tables, watch tables - plus pointers to get_tree and find.

## v2.48.0 (25-07-2026)
- New: tag create_table and watch create_table accept targetFolder and createParents - tables can now be created directly inside folders, same behavior as block creation. Name collisions are checked across all folders and the error names where the existing table lives.
- New: get_tree marks dead branches - [empty] for a folder with nothing in it, [empty subtree] for a branch that holds no items anywhere below; folders with content deeper down get no marker. Footer counts both.
- Note: UDT creation into folders is not yet possible (the underlying API rejects importing new data types); it will arrive together with move_udt. The create action documents this.

## v2.47.1 (25-07-2026)
- Improved: "block not found" errors now suggest close matches ("Did you mean") with each candidate's type, number and folder location, and point to folders get_tree for a full map.
- Improved: if a name belongs to a different kind of object (a tag table, watch table or data type instead of a program block), the error now says so instead of a plain not-found.
- Improved: creating a folder for tags, watch tables, data types, technology objects or external sources now states clearly that items cannot yet be placed into those folders via the API - previously this succeeded silently on a dead end.
- Improved: db get_structure and block get_interface now report the item's folder on a separate line and always show the bare name, however the item was addressed.

## v2.47.0 (25-07-2026)
- New: folders action get_tree - renders the whole folder tree of any project-tree container in one call, with optional contained items (blocks show type, number, language; instance DBs show their owning FB). Use includeItems=false for a quick structure overview, typeFilter=DB to see where your data blocks live.
- Improved: block listings render folder paths with a trailing slash (in: Modbus/) so a top-level folder is visually distinct from the first segment of a deeper path.

## v2.46.2 (25-07-2026)
- Improved: get_references now shows folder paths for every block inside the reference table, not just the subject block in the header.
- Improved: block-number collision errors use one consistent wording everywhere: "already exists at '<folder path>'" for blocks in folders, "at the PLC root" otherwise. Instance-DB number collisions previously gave no location at all.

## v2.46.1 (25-07-2026)
- Improved: cross-reference results (unused blocks, callers, orphaned instance DBs) now show each block's folder path (e.g. Motors/Drives/FB_Pump) so findings can be located immediately.
- Improved: get_references accepts folder-qualified block names and reports the target block's location.

## v2.46.0 (25-07-2026)
- Fixed: data block listing, block consistency check, compiler message fallbacks, and the interface summary now see blocks inside folders. Previously these only looked at the Program-blocks root, so counts and verdicts silently missed foldered blocks on organized projects.
- Improved: listings and consistency results now show each block's folder path (e.g. Motors/Drives/FB_Pump), so you can find what they report.
- Improved: block-number collision checks now catch collisions with blocks living inside folders and name their full location.
- Docs: publish-to-library descriptions updated - blocks anywhere in the project tree can be published, including folder-qualified names.

## v2.45.2 (25-07-2026)
- Improved: requesting an STL block via create_fb/create_fc/create_ob now returns a clear "UnsupportedLanguage" message up front. STL is not supported by the TIA Openness API for block creation (and S7-1200 CPUs do not support STL at all); previously the request failed late with a confusing internal error.
- Docs: tool descriptions updated to list SCL, LAD, FBD as the supported creation languages; removed a stale warning about SCL organization blocks (fixed some releases ago).

## v2.45.1 (25-07-2026)
- Fixed: creating an SCL function block into a folder produced a broken, non-compiling block (the built-in template referenced a tag that does not exist in your project). SCL blocks created into folders now start clean and empty.
- Fixed: STL blocks created into folders could be rejected for the same template reason.
- Improved: when block creation is refused because the name already exists, the error now tells you where the existing block lives.
- Improved: copy_block without newName now returns a clear "newName is required" message instead of an internal error.

## v2.45.0 (25-07-2026)
- Added: create_fb/create_fc/create_ob accept targetFolder (and createParents) to create a block directly inside a program-blocks folder, e.g. 'Motors/Drives'. Missing folders are an error unless createParents=true
- Added: import_xml_inline/import_xml_file accept targetFolder (and createParents) to import a block directly into a folder; without it, overwrite imports still land in the block's own folder automatically
- Added: new action move_block -- moves a block into another folder (or the root) with automatic restore to the original folder if anything fails mid-move; result is verified and recompiled
- Added: new action copy_block -- duplicates a block under a new name (checked against every folder, since block names are unique across the whole CPU), lands next to the source block or in a given targetFolder, and receives the next free block number of its type

## v2.44.0 (25-07-2026)
- Changed: automatic block numbering now assigns the lowest free number of the block type (matching TIA Portal's own behavior), instead of highest-plus-one -- both creation paths now produce identical numbering. OBs keep their floor at 123 (numbers below are reserved by the system)

## v2.43.0 (25-07-2026)
- Fixed (critical): automatic block numbering ignored blocks inside folders, which could assign an already-used number and invalidate the program (flagging unrelated blocks inconsistent). The allocator and the number-collision check now consider every block in the CPU
- Fixed: network edits (update/add/delete) and DB member operations on blocks inside folders failed on reimport with a name-collision error -- the round-trip now writes the block back into its own folder
- Fixed: after a network edit or similar round-trip, the block is recompiled automatically so it no longer stays flagged inconsistent
- Fixed: failed network-edit reimports now return a clear error stating that NO changes were applied, instead of success text followed by an error

## v2.42.1 (25-07-2026)
- Fixed: creating an FB with language LAD or FBD failed with a namespace error (the internal FB template carried a text-language network source) -- graphical FB creation works now
- Fixed: the "block already exists" refusal no longer ends with a success marker or triggers a compile of the existing block -- it is a clean error stating the block was NOT created

## v2.42.0 (25-07-2026)
- Fixed: creating an FB/FC/OB with language LAD or FBD silently produced an SCL block -- graphical languages now use the correct creation path and the requested language is honored
- Fixed: creating a block whose name already exists inside a folder failed late with a cryptic import error and left the existing block flagged inconsistent -- the name collision is now detected up front (block names are unique across the whole CPU regardless of folders) and the error names the folder where the block lives
- Fixed: export-all-blocks no longer leaves empty folders behind when a block fails to export

## v2.41.0 (25-07-2026)
- Added: blocks inside project-tree folders are now reachable -- reading, exporting, compiling, deleting, interface queries, network edits, DB member operations, watch references, and publishing all find blocks at any folder depth, by bare name or by folder path (e.g. "Motors/Drives/FB_X")
- Added: block listing is recursive and shows each block's folder; single-block details show a Folder line; flat projects look exactly as before
- Added: export-all-blocks now includes nested blocks and mirrors the folder structure as subdirectories on disk
- Safety: when the same block name exists in several folders, tools report the ambiguity and list every location instead of silently picking one

## v2.40.2 (24-07-2026)
- Corrected: the folder documentation previously stated moving items between folders is impossible through the API; there is no single-step move, but an export-and-reimport based move is feasible and is planned for a future version

## v2.40.1 (24-07-2026)
- Fixed: a false "RESTART REQUIRED -- License changed" warning was appended to every tool response after the folders tool was added (an internal tool count had not been updated)
- Fixed: get_info now lists the folders tool in its workflow hint, and notes that the TIA connection must be re-established (session connect) after restarting the AI client
- Documented: folder rename is not possible through the TIA Openness API (the folder name is read-only) -- rename folders in the TIA Portal UI; moving items between folders is likewise not exposed by the API

## v2.40.0 (24-07-2026)
- Added: new "folders" tool -- create, list, rename, and delete folders (groups) in the PLC project tree, across Program blocks, Technology objects, External source files, PLC tags, PLC data types, and Watch and force tables
- Added: nested folder paths (e.g. "Motors/Drives") with explicit opt-in for creating missing parent folders -- intermediates are never created silently
- Added: folder listing shows the full tree with item and subfolder counts per folder
- Safety: deleting a non-empty folder requires an explicit flag plus a confirmation phrase, because deletion permanently removes everything inside; folder create/rename/delete require offline mode and fail fast with clear guidance when online

## v2.39.0 (24-07-2026)
- Fixed: refusing a data block reinitialization now returns the intended "DOWNLOAD BLOCKED -- retained runtime data will be LOST -- retry with reinitializeDataBlocks=true" error, instead of an internal prompt-handling failure
- Improved: DB member edits that fail due to export inconsistency (common on consecutive edits) now automatically compile and retry once, annotating the recovery in the result

## v2.38.0 (24-07-2026)
- Fixed: downloads could fail with "Connect to module failed" (or in one diagnostic path crash TIA Portal) because a programmatically created connection address is not always registered by TIA Portal. Downloads now verify the address actually registered before using it, and otherwise connect through the network interface directly -- the method that has always worked
- Improved: configure_connection now reports whether the address actually registered, and explains how the download will connect either way
- Improved: download_check and configure_connection no longer treat TIA's "IsConfigured" flag as proof of readiness (it can be true with no addresses configured); address counts are shown instead
- Fixed: the remembered network interface preference is now cleared when the TIA Portal connection changes, instead of surviving a TIA restart with stale information
- Improved: if a download hits a disposed-object error, the result now instructs how to recover the session instead of failing opaquely

## v2.37.0 (24-07-2026)
- Added: diagnostic connectionVariant parameter for download_to_device -- selects which connection-establishment method the download uses (station, address, or interface form). If a download times out with "Connect to module failed" while TIA Portal itself downloads fine, retry with connectionVariant=address
- Improved: download failure output now lists every configured connection address with counts per interface, and reports enumeration problems instead of hiding them
- Improved: the connect-failure hint now lists both known causes in evidence order

## v2.36.0 (24-07-2026)
- Improved: configure_connection now reports honestly that the applied configuration cannot be verified live (the Openness API is write-only here), instead of implying a confirmed connection
- Improved: download connection failures now include a dump of the live connection configuration and, on connect timeouts, a hint to check the CPU protection level (S7-1200/1500 firmware V4.5+ require an access level/password for write access)
- Improved: when the download connection configuration is missing (it may not survive an online/offline cycle), the error now points to configure_connection instead of the TIA Portal wizard
- Improved: upload_check clarifies that its configuration is separate from the download connection -- "no addresses" there does not mean download is unconfigured
- Improved: saving the project while a device is online now returns a clear "requires offline mode -- run go_offline first" error instead of crashing with a raw exception

## v2.35.0 (24-07-2026)
- Fixed: download interface auto-selection could pick a VPN/virtual adapter over the physical network card, causing "Connect to module" failures. Auto-select now prefers the interface set up by configure_connection in the current session, and virtual adapters (VPN TAP, loopback, hypervisor NICs) always lose ties against physical adapters
- Improved: download output now reports which network interface was selected and why (PcInterfaceSelect line)

## v2.34.0 (24-07-2026)
- Added: new diagnostics action get_device_ip -- reports the IP address configured for a device in the open project (the default download target), plus all of the device's network interfaces with IP, subnet mask, and attached subnet. Answers "which IP does this PLC use" without a network scan

## v2.33.0 (24-07-2026)
- Fixed: download_check no longer reports READY when the compile check could not run (e.g. device online) -- it now returns an explicit "COMPILE UNVERIFIED" verdict with instructions, distinguishing "compile failed" from "compile could not run"
- Improved: block create/import operations (create_fb/fc/ob, create_block, import from XML, add_network) now fail fast with a clear "requires offline mode -- run go_offline first" error when the device is online, instead of failing late with a cryptic XML import error

## v2.32.0 (24-07-2026)
- Added: downloads now target the PLC IP address configured in the open project by default -- if the download connection has no address yet, it is created automatically from the project's hardware configuration
- Added: configure_connection no longer requires a target IP -- omitted, it uses the project-configured IP of the device; pass an IP explicitly only to target a different address
- Added: when the configured connection points at a different IP than the project's hardware configuration, download output flags the mismatch with instructions to repoint -- protects against downloading to the wrong PLC when several devices share the same name
- Improved: download_check now shows the project-configured IP alongside the connection's configured addresses
- Added: new reinitializeDataBlocks parameter for download_to_device -- explicit consent for data block reinitialization after a DB restructure. Consenting stops the PLC and resets retained values to their start values; without consent the download fails with a clear "DOWNLOAD BLOCKED" error explaining what would be lost and how to retry. Previously such downloads aborted with an unanswerable prompt and no path forward

## v2.31.0 (24-07-2026)
- Changed: hardware and hardware_software downloads no longer stop the PLC silently -- they now require explicit stopModules=true and return a clear error before touching the PLC if consent is missing
- Improved: every download failure now includes the full configuration prompt log and the effective arguments (what you passed vs. what was applied), instead of losing them on error
- Improved: download prompts that cannot be answered (password required, destructive configs, unknown prompt types) now produce a named error telling you exactly which prompt failed and why, replacing the opaque "Download configuration 'X' was unhandled" message
- Improved: when a download fails because the change requires stopping the PLC, the error now says "DOWNLOAD BLOCKED" with instructions to retry with stopModules=true (and startAfterDownload=true to restart afterwards)
- Improved: stopModules and startAfterDownload accept true/false, "true"/"false", and 0/1; invalid values return a validation error naming the parameter instead of being silently ignored

## v2.30.3 (11-06-2026)
- Added: Enriched telemetry -- every tool call now tracks which AI client, which PLC device (order number, firmware version), and which session UUID for cross-dimensional error analysis
- Added: Three-tier error classification -- `Exception` (unhandled crash), `SoftError` (Openness API failure caught by wrapper), `Guard` (validation caught bad input before API call, e.g. block/device/tag not found)
- Added: Individual error rows in telemetry -- each failed tool call is sent with full context (tool, action, error type, message, device info, client, session) instead of aggregated counts
- Added: In-memory device info cache -- PLC order number and firmware resolved once per device per session, eliminating repeated Openness API walks
- Added: Recursive device resolution (2 levels deep) with device_profiles fallback for hardware identification
- Added: Mandatory version update notification -- when a newer version is available, the AI agent is instructed to inform the user with step-by-step update instructions before proceeding with any task
- Added: Device cross-dimension tracking in telemetry -- device usage grouped by AI client for per-client error rate analysis

## v2.29.0 (10-06-2026)
- Improved: Degraded mode now distinguishes between "TIA Portal not installed" and "TIA Portal installed but Openness API not enabled", providing tailored setup instructions for each case
- Improved: Degraded mode detects and displays the installed TIA Portal version (e.g. V19) even when Openness is missing
- Improved: TiaCommander Manager GUI now launches normally when TIA Portal is not installed (previously blocked with an error dialog), allowing license registration before TIA Portal setup
- Improved: Restart instructions in degraded mode guide users to use Ctrl+Alt+Delete and Task Manager for a clean restart
- Added: Telemetry now tracks degraded mode sessions with reason codes for better install-experience analytics

## v2.28.0 (10-06-2026)
- Added: `hardware action=dump_catalog` — dumps the full TIA Portal hardware catalog (~11,000 entries) to local database. Auto-detects TIA Portal version changes and re-dumps on upgrade
- Added: `hardware action=search_catalog` — live real-time search of TIA Portal hardware catalog (requires TIA Portal running)
- Added: `admin action=search_device_catalog` — offline search of locally cached hardware catalog by article number, type name, or description. No TIA Portal required
- Added: `admin action=reset_device_catalog` — clears local catalog cache and triggers re-dump on next TIA Portal connection
- Added: Device catalog telemetry — hardware catalog entries are included in telemetry data for aggregated device usage insights
- Added: Per-project device catalog collection — walks project DeviceItems recursively to catalog all installed hardware modules

## v2.27.0 (10-06-2026)
- Added: `session action=get_state` — proactive state check returning connection level (0-4), TIA/project/device status, license info, and restart flag. Call at session start for instant situational awareness
- Added: Graceful state guards across all tools — instead of cryptic exceptions when TIA Portal is not connected or no project is open, tools now return clear guidance on what to do next
- Added: Restart detection — if your license is activated or changes mid-session, every tool response includes a restart reminder so the AI assistant picks up the new tool set
- Improved: `session action=info` now includes state level and guidance for non-ready states
- Improved: Server instructions updated to recommend `get_state` as the first call in every workflow

## v2.26.3 (07-06-2026)
- Added: MCPB bundle (.mcpb) in GitHub Releases -- install TiaCommander in Claude Desktop with one click (Settings -> Extensions -> Advanced Settings -> Install Extension)
- Improved: Manager terminal now shows restart instructions after license activation
- Fixed: auto-update now preserves manifest.json and icon.png in MCPB extension directory

## v2.26.1 (26-05-2026)
- Fixed: `open_manager` failing on fresh downloads with "The operation was canceled by the user" — Windows SmartScreen blocks shell-executed processes from internet-downloaded EXEs in headless mode
- Fixed: SQLite database file leaking into release ZIP
- Fixed: release notes encoding for non-ASCII characters (em dashes)

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
