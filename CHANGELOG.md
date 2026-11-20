# Changelog

All notable changes to TiaCommander are documented here.

## v3.46.0 (19-11-2026)
Highlights since the last public release (v3.42.0); every change is listed under its own version below.
- **New: know-how protection for PLC blocks.** Protect and unprotect blocks, with passwords stored encrypted by Windows on this PC and a new Block protection tab in the Manager. Tools refuse to change a protected block or data type before anything is touched.
- **New: the tool set follows your licence live.** Tools appear or drop in the same session when a licence refreshes or changes, and the Manager has a Logout button.
- **Fixed: data types (UDTs) with nested Structs.** They are read and edited correctly, start values stay on their own member, and nested members can be addressed by path (e.g. AxisX.Home).
- **Fixed: names containing '/' work everywhere,** and an ambiguous name is refused, never guessed.
- **Fixed: devices inside device groups** are listed, counted, compiled and can be named in every tool. I/O addresses are reported on the module that owns them.
- **Changed: texts follow the project's editing language.** Block, network, member, tag, watch-table and text-list comments are written in it and other translations are kept.
- **Changed: safer edits.** Every block, data type, DB, tag, watch-table and text-list write is checked after it lands; on a mismatch the original is put back and the reply says so.
- **Fixed: TiaCommander no longer starts a second TIA Portal** when one is already running.
- **Fixed: stored licence data and PLC passwords stay readable after a Windows update.**
- **Fixed: many reports from GitHub,** including LAD/FBD blocks with absolute addresses, Retain on the SCL route, library types that need their PLC data types, and bulk calls.
- **Licensing: internal robustness improvements.**

## v3.45.6 (19-11-2026)
- **Fixed: stored licence data and PLC passwords could become unreadable after a Windows update.** The machine ID that protects them is now worked out once and kept, so a Windows update or a brief hardware-read hiccup can no longer change it. Existing installs keep their current ID and stay readable.

## v3.45.5 (03-11-2026)
- **Fixed: clipped text in the Manager.** The hint in the dialog that shows a stored password is shown in full, and so is the description on the Block protection tab.

## v3.45.4 (03-11-2026)
- **Fixed: know-how passwords of a block with a slash in its name.** A block named `Motors/Pump` and the block `Pump` in the folder `Motors` now keep separate stored passwords. Before, storing one replaced the other, so the first block's unprotect was refused. In the password list, a path part that contains a slash is shown in double quotes.

## v3.45.3 (03-11-2026)
- **Changed: know-how protected data types are status only.** Tools no longer tell you to remove a data type's protection in TIA Portal, because Portal V19 offers know-how protection for blocks only. A protected data type is still detected and refused. (gh#17)

## v3.45.2 (03-11-2026)
- **Changed: know-how passwords are now encrypted by Windows (DPAPI) on this PC** and no longer depend on the machine ID, so a machine ID change cannot make them unreadable while a block stays locked. Another PC or a Windows reinstall cannot read them; such an entry is reported as unreadable, no other stored password is tried, and an explicit password replaces it. (gh#17)

## v3.45.1 (03-11-2026)
- **New: Manager "Block protection" tab.** Add, view, replace and delete know-how passwords per block, per project or global, the same way as PLC passwords. "Show previous" lists replaced passwords, which you can view, restore or permanently delete. (gh#17)

## v3.45.0 (03-11-2026)
- **New: know-how protection for PLC blocks.** `blocks_write` gains `protect` and `unprotect` (one block or a list), and `blocks_read` gains `get_protection` and `list_protected` (blocks and UDTs; a UDT is status only, Openness can neither protect nor unprotect it). (gh#17)
- **New: stored know-how passwords.** `set_protection_password`, `delete_protection_password`, `restore_protection_password` and `list_protection_passwords` keep passwords encrypted by Windows on this PC, per block, per project or global. `protect` saves the password it uses before it locks the block, so a lock can't lose it; replaced or deleted passwords stay in a restorable history until you delete them for good.
- **Changed: passwords stay out of logs.** Argument values not registered for an action are masked before the debug log, the activity log and telemetry.

## v3.44.74 (02-11-2026)
- **Changed: tools refuse to change a know-how protected block or UDT, before anything is touched.** Edits, overwrites, imports over a protected block, delete, rename, move, copy and delete_folder with its contents now reply that the object is protected and how to remove the protection. Reading its interface still works. (gh#17)
- **Changed: exports of a protected block say what they are.** export_xml_file, export_xml_inline and export_all_xml give the public interface only, with a note saying so; export_source refuses. An interface-only export is never used as an overwrite backup.
- **Fixed: a DB member edit could trigger a whole-PLC compile on reply text alone.** The compile and retry now run only when TIA itself refuses the export as inconsistent.

## v3.44.73 (02-11-2026)
- **Internal: encrypted store for know-how protection passwords** (no new tool yet). A password that cannot be read on this PC is reported as unreadable, never used. (gh#17)

## v3.44.72 (02-11-2026)
- **Fixed: network and statement edits no longer fail because a different block is inconsistent.** add_network, update_network, split_network, delete_scl_statement and update_network_element: when TIA refuses the export for that reason, the target block is compiled once and the export is retried. If the target block has compile errors of its own, the edit is refused and those errors are named. (gh#18)
- **Fixed: create_block with overwrite=true on a new name refuses a block number another block already holds.** Overwriting a block of the same name still keeps its number. (gh#25)
- **Changed: empty title and comment items are no longer written when a block or network is created without them** (create_block XML route, add_network, split_network, create_fb / create_fc / create_ob). DB templates are unchanged. (gh#13)

## v3.44.71 (28-10-2026)
- **Fixed: TiaCommander no longer starts a second TIA Portal when one is running.** session open, create, connect and
  launch now look at the running TIA Portal processes first: with one TIA Portal running they attach to it ('Attached
  to running TIA Portal (PID n)'), and launch says so instead of starting another one. When they cannot tell which
  instance to use, or TIA Portal runs but Openness cannot reach it, they refuse at once with the reason and the way out
  - never a wait, never a hidden second instance: several instances -> each PID with its open project and 'retry with
  pid=<n>'; another TIA Portal version than the bound one -> says which version and how to select it; anything else ->
  'TIA Portal is running (PID n) but Openness cannot attach to it' with the common causes. Background helper processes
  of TIA Portal are never mistaken for a running instance.
- **New: session launch forceNew=true** starts a second TIA Portal instance explicitly - the only way to get one.
- **New: pid selects the TIA Portal instance** for session connect, open and create when several run. session open
  also picks, by itself, the one instance that already has the requested project open.
- **Fixed: session disconnect closeTia=true after session launch** closes the TIA Portal this session started (launch
  now records its process id) instead of refusing with AmbiguousTarget.
- **Improved: the state hints no longer suggest session launch while a TIA Portal is running** - get_state, get_info
  and the 'not connected' guard of every tool point to session connect instead.
- **Fixed: open_manager recognises an already running Manager (gh#29).** It checks the Manager's own single-instance
  lock instead of the process name, so another AI client's TiaCommander server no longer makes it answer 'Manager is
  already running' while no Manager window is open.

## v3.44.70 (28-10-2026)
- **Fixed: create_block (SCL) with a member set to remanence=NonRetain is created.** NonRetain is written as a plain
  declaration (the default on an optimized block); the previous build wrote a NON_RETAIN keyword that TIA rejects, so
  the block was not generated. Retain is unchanged (VAR RETAIN).

## v3.44.69 (28-10-2026)
- **Fixed: LAD / FBD blocks with absolute addresses compile (gh#28).** Contacts, coils, box pins and every other operand
  written as an absolute address - %I0.2, %Q1.0, %M101.0, %MW10, %ID4, %DB10.DBX0.0, %DB10.DBW2 - are now created as
  addresses instead of as a tag named '%I0' (TIA's 'Tag "%I0"."2" not defined'). An operand that starts with '%' but is
  not one of these forms is refused with the list of supported forms, never written as a broken tag. Networks that hold
  such addresses can also be read back and edited with the rung tools, and a box pin can be rewired to an absolute address with update_network_element.
- **Fixed: a library FB / FC that needs its PLC data types instantiates and is consistent (gh#19).** library
  instantiate_library_type now creates the PLC data types the block's library version depends on first - at exactly the
  version it depends on, unless a type of that name is already in the PLC - and compiles the new block once when TIA
  reports it inconsistent. The reply lists each dependency (created or already present). When the block cannot be
  created after its data types, they are removed again and the block is created the way it was before, with a warning.
- **Fixed: Retain survives the SCL route (gh#15).** create_block (SCL) writes remanence=Retain / NonRetain into the
  block (VAR RETAIN for FB and DB members of Input / Output / InOut / Static on an optimized block; NonRetain is the
  default there and is written as a plain declaration),
  and an overwrite through the SCL route now keeps the members' External accessible / visible / writable and Setpoint
  flags and the block's own attributes, verified like the LAD / FBD route (a carry that does not verify restores the
  original block). The 'not applied' note now names only what is still not applied.
- **Fixed: blocks_read export_xml_file and export_source overwrite the file at outputPath (gh#30).** A second export
  to the same path no longer fails on the existing file (the stale-file trap in the report); the reply says when an
  existing file was overwritten.
- **New: session action=get_languages (gh#30)** - read-only: the open project's languages, active languages, editing
  language and reference language, as culture names. Needs an open project, no device.
- **Fixed: hardware search_catalog and dump_catalog work in a project without devices (gh#30)** - they need only a
  connected TIA Portal now; every other hardware action still needs a device.
- **Improved: the log carries the full cause of an Openness error, and TIACOMMANDER_LOG_DEBUG is documented (gh#30).**
  Failed Openness calls (imports, exports, source generation, library instantiation, and any tool error) are logged
  with every inner exception and its stack trace, not only TIA's generic 'Error when calling method ...' text; library
  instantiate_library_type replies with the inner cause, and its 'unique per device' hint appears only when that is the
  cause. README: where the logs are and how to turn on debug logging (TIACOMMANDER_LOG_DEBUG=1).

## v3.44.68 (28-10-2026)
- **Fixed: an edit is no longer refused or rolled back because ANOTHER block has compile errors (gh#18 remainder).**
  db member edits (add_member / add_members / update_member / delete_member(s) / set_start_values) that recompile after
  an export inconsistency, db get_member and diagnostics snapshot_read now judge only their own DB: compile errors that
  belong to other blocks are named in a NOTE and no longer stop the edit or the read. move_block and the create_block
  SCL route with targetFolder do the same for their block. Every overwrite that verifies by compiling - network and
  member edits, blocks_write import_xml_file / import_xml_inline with overwrite, the create_block overwrite (SCL and
  XML, incl. its carried attributes) and the db edit compile check - now rolls back only when the edited block itself
  has compile errors (a guard: the reported rollback of a good edit could not be reproduced on the test bench). The
  reply says 'Verified: 0 error(s) in '<block>' itself' and names any other objects' errors in a NOTE. Restores after
  a failed edit or move are judged the same way.
- **Fixed: move_block never reports a plain success for a block that does not compile.** The moved block's own
  compile result is read; a block with compile errors of its own is flagged with a WARNING (the move itself is kept and
  verified), errors in other blocks are only named.
- **Fixed: create_block (SCL) with targetFolder reports a compile failure as such.** A created block that does not
  compile is left at the block root with its compile errors listed, instead of a misleading move / export failure.
- **Fixed: udt move keeps the data types and blocks that use the moved UDT consistent (gh#31).** After the move every
  UDT and block that uses it (directly or through another UDT) is recompiled, so e.g. a UDT that contains the moved UDT
  can be exported straight away without a manual compile_all. The reply names the dependents recompiled, or warns with
  any that still have compile errors of their own; a compile error elsewhere in the project never turns a landed move
  into a failure.
- **Fixed: udt import_xml overwrite=true works for a UDT in a subfolder and is protected (gh#22).** The UDT is replaced
  in its own folder (no more 'fully qualified name already exists'), a backup is taken first, the result is verified
  (found in place and compiling) and a failed verify is rolled back from the backup. overwrite=false on an existing UDT
  is refused before any change, as before. The same applies to the xmlContent and exportId forms.
- **Fixed: blocks_write import_xml_file / import_xml_inline with a PLC data type document (gh#22)** import it as a UDT:
  targetFolder names a folder of PLC data types (createParents creates it there), not a Program blocks folder.
- **Improved: an overwrite import of an inconsistent block takes a backup when it can (gh#30).** The target is compiled
  once first; if it then exports, the overwrite is protected by a backup. A target that still cannot be exported is
  imported as before, with the no-backup warning (an import is never refused for that).
- **Clearer move failure hint (gh#30).** When udt move, tag move_table or watch move_table cannot export the item
  because it is inconsistent, the reply now says to compile first (blocks_read action=compile_all) and retry; the UDT
  export paths use the same hint.

## v3.44.67 (28-10-2026)
- **Fixed: names containing '/' now work everywhere (gh#24).** A block, PLC data type, tag table, watch table, library
  type or master copy whose name contains '/' (e.g. 'EtherNet/IP') is found by that name - it is no longer read as a
  folder path and reported as not found. One pair of surrounding quotes and '\' for '/' are still accepted, and
  'Folder/Name' still finds an object in a folder.
- **An ambiguous name is refused, never guessed.** When a name matches more than one object (for example a block named
  'Motors/Pump' at the root and block 'Pump' in folder 'Motors'), the reply lists every candidate with its folder and a
  selector that picks exactly that one: `literal:<name>` (the exact name in any folder), `path:<folder>/<name>` (a
  folder segment that itself contains '/' in double quotes, e.g. `path:"Line/1"/Pump`) or, for blocks,
  `number:FB12`. Every name argument accepts these selectors. This holds for move_block and copy_block and for the
  block actions that used to answer such a name with 'not found' (rename, title / comment, networks, calls, DB delete,
  instance DB create, library publish, multi-instance members), and for udt action=move, tag move_table and watch
  move_table, which now also accept 'Folder/Name' and the selectors.
- **Fixed: UDT edits and tag table imports / moves never act on a look-alike.** A UDT edit or create re-reads its type
  by its exact name, so a UDT named 'A/B' is backed up and verified even when a UDT 'B' sits in folder 'A' (before, the
  edit could run with no backup and then report the type as not found). tag import_table reads the table name in the
  XML as an exact name and refuses, with NO changes applied, when that name cannot be resolved cleanly; tag move
  re-reads both tables by their exact names.
- **Fixed: a create, move or copy never touches another block with a similar name.** The clean-up after TIA refuses a
  new block, the checks after a move or copy, and their compile steps look the block up by its exact name. Before, an
  unrelated block 'Pump' in folder 'Motors' could be deleted when a block named 'Motors/Pump' was created or moved.
- **Fixed: auto-compile after an import uses the imported block itself (gh#24).** import_xml_inline / import_xml_file
  compiled a block whose name was read back out of the reply text - a name containing ' (' was cut short and another
  block was compiled. The compile now uses the block the import returned; create_fb / fc / ob and create_block do the
  same.
- **Fixed: block, UDT and table names with '/' or ':' no longer break temporary and export files** (interface edits,
  XML edits, moves, copies, backups, export_source, export_all_xml).
- **Fixed: create_fb / create_fc / create_ob / create_block with a '/' in the name** check for an existing block by
  that exact name and create it under that name - they no longer switch to another creation route or look at a block
  in a folder.
- **Fixed: an OB created from the default template no longer carries the title with literal quotes (gh#25)** - the
  title is Main Program Sweep (Cycle).

## v3.44.66 (28-10-2026)
- **Fixed: bulk calls were refused for a "missing" single-item argument (gh#26).** tag add_tag / delete_tag, udt and
  db add_member / delete_member and blocks_write add_multi_instance_member now accept the array form alone (tags[],
  tagNames[], members[], memberNames[]) - an empty array still counts as missing. udt create, udt add_member and db
  add_member accept each member item as {memberName, dataType} or {name, dataType}.
- **Clearer action and argument texts (gh#29).** Every action's argument list now says which arguments are required,
  which are alternatives and which are optional, with the real defaults; error and hint texts no longer name actions
  that do not exist; every "unknown action" reply lists exactly the actions the tool offers. A missing required argument
  names the action that lists valid values for it, and a missing confirm names the exact phrase to pass.
- **Fixed: alarm_text add_entries / update_entries without entries[] (and delete_entries without fromValues[]) are
  refused naming the argument** instead of running and reporting "0 entries added".
- **Fixed: blocks_write import_xml_inline without xmlContent is refused naming xmlContent** instead of failing with an
  internal error.
- With more than one AI client attached, library delete_unused_types without dryRun is a preview again (it never
  deletes without dryRun=false), so it no longer asks for the multi-client confirmation.

## v3.44.65 (27-10-2026)
- **Fixed: usage statistics and licence checks could fail with "Could not create SSL/TLS secure channel" when
  TiaCommander was started through another program.** HTTPS calls now always use TLS 1.2 or 1.3, whichever program
  starts TiaCommander. A normal installation was not affected and behaves exactly as before.

## v3.44.64 (27-10-2026)
- **Fixed: usage statistics stopped reaching the TiaCommander service after an error text contained a control
  character.** An error message that quotes an invisible character (for example TIA's "Invalid XML ... hexadecimal
  value 0x01") was sent unescaped, the service rejected the whole report, and because a failed report is retried with
  the same content, no later report got through either. Every such character is now escaped, so the next report goes
  through and carries the backlog. The licence check and the Manager's registration form use the same escaping (a tab
  pasted into a name no longer breaks registration). A failed network send now logs its cause.

## v3.44.63 (24-10-2026)
- **Fixed: on a modular S7-1500 the device's order number is the CPU's, not the rail's - live_data no longer refuses
  a correct CPU as a different device (gh#11).** live_data read_tag_table / read_watch_table compare the named
  project device with the CPU they are connected to. The project side took the first module carrying an order
  number, which on a modular S7-1500 is the mounting rail (6ES7 590-...), so a connection to the right CPU was
  refused with "DeviceMismatch". The check now uses the CPU module's order number; S7-1200 and other devices keep
  the order number they reported before.

## v3.44.62 (24-10-2026)
- **Fixed: I/O addresses are reported on the module that owns them (gh#20).** hardware get_device, get_full_config,
  get_rack_slot_details and get_io_map listed every I/O range of the PLC's IO system on the CPU line - the ranges of
  the CPU's own signal modules, of ET 200SP stations and of GSD devices - and left the modules themselves without an
  address. Each range now appears on its own module (or GSD submodule), written exactly from start bit to end bit
  (I10.0-I11.7; several joined by "; "), and get_io_map lists one row per range for every device, including devices
  in device groups and in "Ungrouped devices". The devices CSV / XLSX export has one row per range (IoType,
  StartByte, StartBit, LengthBits, Address) and still lists a device that has no address.
- **New: get_io_map takes a deviceName and get_rack_slot_details a slot filter (gh#20).** Omit them for the full
  view as before; an unknown or ambiguous deviceName is refused like in every other tool, and a slot the device does
  not have is an error that lists the device's slots.
- **Fixed: get_assignment_list / find_next_free count I/O devices on the PLC's IO system.** Hardware rows now name
  the device and module that occupy each byte ("HW: ET200SP_1/DI_1") instead of the CPU, devices on another PLC's IO
  system are not counted, and find_next_free never hands out an address an IO device already uses.

## v3.44.61 (24-10-2026)
- **Fixed: devices inside device groups can now be named in every tool (get_device, compile_device, set_network_config,
  get_plc_status, exports ...) (gh#21).** A device in a device group or in "Ungrouped devices" was listed since the last
  release, but naming it in a tool still answered "not found". Every tool that takes a deviceName now finds it, and
  also accepts the group path (Group_A/Sub_B/Device), the station name, the PLC software name and the head-module
  name; a name that fits several devices is refused with the candidates and where each one lives, and a name that
  fits none lists every device with its group and head module. hardware get_full_config shows every device;
  export_io_map and export_hardware_map without a deviceName export every device; get_assignment_list and
  find_next_free keep the hardware I/O rows when the device was named by an alias. When a name was resolved to a
  device and the call then failed, the error reply now says which device it meant. compile_device refuses an
  ambiguous name instead of compiling a station that merely holds an item of that name.

## v3.44.60 (24-10-2026)
- **Fixed: devices inside device groups and in "Ungrouped devices" are now listed, counted, compiled and exported
  (gh#21).** Until now these tools saw only the devices at the top level of the project tree. session list_devices
  now lists every device and shows where it lives ("Group: Group_A/Sub_B", "Group: Ungrouped devices") and the name of
  its head module ("Head module: ..."); session get_project, get_state and open count every device;
  a project whose devices are all inside groups is no longer reported as "Project has no devices"; hardware
  compile_all compiles grouped and ungrouped stations too; the devices CSV / XLSX export has their rows;
  hardware get_network lists their network interfaces; library update_project and delete_unused_types (dry run)
  also look inside PLCs that sit in a device group (a type used only there is no longer reported as unused); the
  live_data PLC address list and the scan's project IP map include them; the check that refuses to save while a
  device is online covers them too. Looking a grouped device up by name in the other tools follows in the next release.

## v3.44.59 (24-10-2026)
- **Internal groundwork for device groups (no behaviour change).** TiaCommander gains one shared component that
  sees every device of a project - top-level devices, devices inside device groups (nested to any depth) and
  "Ungrouped devices" - and resolves a device by its name, its group path (for example "Group_A/Sub_B/PLC_Station"),
  its station name, its PLC name or its head-module name, refusing ambiguous names with the candidates listed. No
  tool uses it yet; the following releases move the tools onto it so grouped and ungrouped devices stop being
  missed. At startup, a TIA Portal Openness version without device-group support is now reported as incompatible
  instead of silently listing only part of a project's devices.

## v3.44.58 (19-10-2026)
- **Fixed: create_block with overwrite failed for an existing SCL block ("Access to a disposed object").** In a
  project with more than one language, overwriting an existing SCL block (create_block language=SCL
  overwrite=true) was refused with "Access to a disposed object of type ...FC" and the original block was put
  back unchanged. The overwrite now completes: the new code is kept, the block title and comment keep their
  other languages, and the reply names the language written. No released version was affected: the error came
  with the language carry added in v3.44.53 (never deployed); v3.44.45 and earlier do not run that step.

## v3.44.57 (19-10-2026)
- **Fixed: create_block with overwrite=true reported that the block title and comment were kept in every
  language even when no block of that name existed.** The call then simply creates the block, and the reply now
  says so ("no block named ... existed, so it was created new"). When the block does exist, the reply says that
  its title and comment were kept and read back. If that read-back differs, the call fails and names where the
  copy of the block from before the overwrite was kept.
- **Changed: the create_block reply names the language it wrote in the same form as the other write replies**,
  for example "Texts: title, comment and member comments written (culture de-DE)."

## v3.44.56 (19-10-2026)
- **Fixed: interface member comments, library texts and network titles were shown empty when only the project's
  editing language had text, or showed another language's text instead.** get_interface (member comments),
  library get_type (the type comment) and get_network / get_edit_capabilities (network title and comment) now
  show the text of the project's editing language. When that language has no text, they show the first language
  that has text, marked with its language (for example "[en-US] Motor on"). A library type comment no longer
  prefers en-US; with no project open, the first language that has text is shown, marked. The expectTitle check of
  the rung edits compares the title text without that marker.

## v3.44.55 (19-10-2026)
- **Fixed: blocks created from the built-in templates (create_fb, create_fc and create_ob in LAD or FBD or with a
  target folder, db create with a Standard memory layout or a target folder, and the template fallback of every
  SCL create) still carried en-US texts.** Their block and network title and comment items, and an OB's input
  comments, are now created in the project's editing language, so a project edited in another language no longer
  gets en-US entries, and a project without en-US no longer refuses the create with "Cannot import multilingual
  text with culture en-US". The reply names the language written. When the editing language cannot be read the
  create is refused before anything is changed.

## v3.44.54 (19-10-2026)
- **Fixed: adding a force table entry without a comment failed.** TIA Portal refused the import with "The value of
  attribute 'Comment text' contains an invalid character at Position 0", because the empty comment was saved with
  a line break inside it. The same affected watch table entries added without a comment, and clearing an entry's
  comment, modify value or force value with update_entry. An empty value is now saved as empty.

## v3.44.53 (19-10-2026)
- **Fixed: blocks, OB inputs and networks were created with texts in en-US instead of the project's editing
  language; on a project without en-US they were refused (gh#13).** create_block (title, comment, member
  comments and the Initial_Call / Remanence comments of a program-cycle OB), add_network (single and bulk) and
  split_network now write their texts in the project's editing language. An empty title or comment is created
  as an empty text in that language. Every reply names the language it wrote. When the editing language cannot
  be read, the call is refused before anything is changed.
- **Fixed: overwriting a block with create_block lost the block title and comment in the other languages.** An
  overwrite now keeps the block title and comment in every project language: a title or comment you supply
  replaces only the editing-language text, one you leave out is kept as it was in all languages. The texts are
  read back after the overwrite. If the old title and comment cannot be read first (for example because the
  block does not compile), the overwrite is refused and nothing is changed. Network titles and comments and
  interface member comments are still replaced by an overwrite, and the reply says so.
- **Changed: update_network and replace_network now change only the editing language, and an empty value
  clears.** A title or comment now updates (or adds) the text of the project's editing language and leaves the
  other languages untouched; before, it overwrote whichever language came first. Passing an empty title or
  comment ("") now clears that text in the editing language instead of being ignored; leaving the argument out
  keeps the text unchanged. split_network renames the first network in the editing language the same way.

## v3.44.52 (19-10-2026)
- **Fixed: watch and force table entry comments were written in en-US and read back empty when only the editing
  language had text.** Adding or updating a watch or force table entry now writes its comment in the project's
  editing language and leaves the comment's other languages as they were; the reply names the language written.
  Reading entries (get_entries) shows the editing-language comment, or the first language that has text, marked
  with its language (for example "[en-US] Motor on"). The CSV and XLSX exports carry the comment text without that
  marker. A new comment on an entry that had none is written with valid IDs, and the watch comment description no
  longer recommends ASCII.

## v3.44.51 (19-10-2026)
- **Fixed: create_textlist without a comment failed on projects with more than one language.** Since v3.44.49,
  creating a text list with entries but no comment was rejected by TIA Portal ("No valid language was found for
  the import") and nothing was created. The new text list now always carries a comment cell in the project's
  editing language (empty when no comment is given), as it did before v3.44.49, so the import is accepted. The
  comment and the entry texts still go into the editing language only, and the other languages are left empty.

## v3.44.50 (19-10-2026)
- **Changed: project and library names may contain non-ASCII characters.** The ASCII-only limit is gone now that
  tool input is read as UTF-8. Creating a project or saving it under a new name no longer refuses names with
  characters such as Latvian diacritics or Cyrillic, and the tool descriptions no longer tell clients to use
  ASCII names.

## v3.44.49 (19-10-2026)
- **Fixed: update_textlist_comment overwrote the comment in every language of the project.** A text list comment
  is kept once per project language. The new comment was written into all of them, so every translation was
  replaced by the one text sent. Now only the comment in the project's editing language is written, every
  language is read back afterwards, and the reply names the language it wrote, e.g. "(culture de-DE)". If any
  other language changed, or the new text did not land, the previous texts are written back and read again, and
  the reply starts with "Error:" and says whether every language was restored (read back) or which language
  could not be restored and what it now holds. If the editing language cannot be read, nothing is changed.
- **Fixed: create_textlist / add / update entries wrote the text into the last language column instead of the
  editing language.** On a project with more than one language, the comment of a new text list and the text of
  new or updated entries went into whichever language came last in TIA's export, not into the project's editing
  language. They now go into the editing language only, and the reply names it. If the export has no column for
  the editing language, or the editing language cannot be read, the call is refused and nothing is changed.

## v3.44.48 (19-10-2026)
- **DB and interface member comments are written in the project's editing language.** `db add_member` (single and
  bulk), `db update_member` and the block interface member editor (`add_interface_member`,
  `update_interface_member`) used to write member comments as English (en-US), and an update changed whichever
  language came first. They now write the language the project is edited in and keep every other translation as it
  was. The reply names the language it wrote, e.g. `(culture de-DE)`. If the project's editing language cannot be
  read, the call is refused before anything is changed.
- **Clearing a member comment keeps the other translations.** `newComment=""` used to delete the comment in every
  language. It now clears only the editing language; the comment is removed completely only when no other language
  still has text.
- **DB member views show the editing language.** `db get_member` and `db get_structure` show member and DB comments
  in the project's editing language. When that language has no text, the first language that has text is shown,
  labelled with its language (e.g. `[en-US] Motor speed`). `db get_structure` no longer runs every translation of a
  member comment together into one string, and shows each member's own comment rather than the first comment found
  inside it.

## v3.44.47 (19-10-2026)
- **Fixed: non-ASCII tool arguments (e.g. Polish/Latvian letters) arrived garbled on Windows with a non-UTF-8 code
  page (gh#16).** A name or comment such as `Janis Berzins` written with its accents reached the project as
  unreadable characters when the machine's console code page was not UTF-8. TiaCommander now reads every request
  as UTF-8 regardless of the code page, on the direct connection and when a second client shares the running
  instance. A request that starts with a UTF-8 byte-order mark is accepted. Requests to the live-data component are
  also sent as UTF-8.

## v3.44.46 (19-10-2026)
- **Internal: one shared way to read and write texts in a chosen project language.** No change in behaviour.
  Tag comments, UDT member comments and the IDs of new XML elements are now handled by one set of helpers that
  write a single language and leave every other language as it was. Reading a tag comment or a UDT member
  comment still returns the text in the project's editing language, else the first language that has text, as
  before. This prepares the coming fixes for comments and titles written in the wrong language.

## v3.44.45 (19-10-2026)
- **Fixed: text-list create / entry edits / import were undone on projects with more than one language.** On a
  project with more than one active language, TIA writes the text "<No value>" into every comment or text cell
  of the exported text lists that has no text. The check that runs after create_textlist, add_entries,
  update_entries, delete_entries and import_textlists compared that against the empty text it expected, saw a
  difference, put the text lists back and replied with "Error: ... was NOT applied". A cell that holds exactly
  "<No value>" now counts as empty, on both sides of the check and when the text lists are put back after a
  failed edit. Any other text is still compared exactly: a text that did not land is still a failed edit, and
  "<No value>" as part of a longer text is kept as written.

## v3.44.44 (18-10-2026)
- **Changed: watch and force table entry replies show the entry as TIA stored it.** TIA stores some values in its
  own spelling: an absolute address in upper case (%mw2752 is stored as %MW2752), TRUE and FALSE in upper case (a
  force or modify value sent as true is stored as TRUE), and a DB member written without quotes with the DB name
  quoted (NonOptimised.test1 is stored as "NonOptimised".test1). An add_entry is verified against the stored
  table and keeps these forms - the entry is added, the reply says "verified", and it shows the stored address,
  modifyValue and forceValue. Only these spelling differences are accepted: a different address, a different
  value (16#ff is not 16#FF, true is not 1) or a different tag name case is still treated as a failed edit, and
  the table is put back from its copy.
- **Changed: update_entry refuses an empty displayFormat.** TIA does not accept an entry with an empty display
  format, so update_entry with displayFormat "" (or only spaces) is now refused before anything is written, and
  the reply says "NO changes were applied" and lists the valid display formats. Leave displayFormat out to keep
  the entry's stored format. add_entry without a displayFormat still uses Hex.

## v3.44.43 (18-10-2026)
- **Fixed: adding, updating or deleting a watch or force table entry, and clear_table, could leave the table
  replaced with no copy to go back to.** Each of these edits now exports the table first; if that copy cannot be
  taken, the edit is refused and the reply says "no backup could be taken ... NO changes were applied". After the
  edit the table is read back and compared row by row with what it should now hold: every row that was there
  before, with only this call's change applied (two rows with the same address are both kept and checked). The
  row check accepts the same stored forms as watch import_table (a tag name written without quotes is stored with
  quotes, an absolute address that belongs to exactly one tag is stored as that tag's name, the display format
  Date is stored as DATE_AND_TIME). The reply says "verified" only when the table holds exactly the expected rows.
  When it does not, the table is put back from the copy, checked again, and the reply starts with "Error:" and
  says the table was RESTORED; if even that fails, it says the table could NOT be restored and names the copy
  ("Backup KEPT at: <path>").
- **Changed: a display format that TIA replaces is named in every add_entry and update_entry reply.** When the
  display format you send does not suit the address's data type, TIA stores another one (for example Float on a
  single bit is stored as Bool, Bool on a word as Hex). This is still accepted for the entry the call writes, and
  the reply now says so in one place: "displayFormat: stored 'Bool' (SENT 'Float' - TIA's documented fallback for
  the target's type)", and its "verified" part reads "(display format excepted, see above)". update_entry, which
  did not mention it before, now names it too (an empty displayFormat is refused - see v3.44.44). A
  format change on any other row of the table is not accepted: the edit is undone as described above. Choose the
  display format that matches the address's data type to have it stored as sent.

## v3.44.42 (18-10-2026)
- **Internal: test hook for watch and force table entry edits.** No change in behaviour. A test-only switch
  lets the test build fail an entry add, update, delete or a clear_table right after the table was written,
  so the next release can be checked against what such an edit leaves behind when it fails at that point.
  The switch is off in every normal installation.

## v3.44.41 (18-10-2026)
- **Fixed: a failed text-list write could leave the text lists changed, with no copy to go back to.**
  alarm_text create_textlist, add_entries, delete_entries, update_entries and import_textlists
  now export every text list of the device before they write, read every list back afterwards
  and compare it with what was asked: the list name, range and comment, and for every entry
  From, To and the text in every language. A list the file names but carries no entries for
  keeps its entries. The reply says "verified" only when the lists the write names hold the
  change and every other list is unchanged. When the write did not land as asked, every text
  list is put back from that copy and checked again, and the reply starts with "Error:" and says
  the lists were RESTORED. If even that fails, the reply says the lists could NOT be restored and
  names the file that holds the copy ("Backup KEPT at: <path>") and the other files of that write
  it kept.
- **Fixed: an import whose state was not OK was not reported as an error.** A text-list write
  whose import ends in a warning or error state is now a failed write: the lists are restored as
  above and the reply starts with "Error:" (import_textlists still names the import state, the
  file and the import log).
- **Changed: a text-list write is refused when no complete copy can be taken.** The copy of every
  text list must come back from TIA without a warning or error and must hold every user text list
  of the device; otherwise nothing is written and the reply starts with "Error:". An undo removes
  only lists the failed write added.
- **Changed: a text-list write that changes a system text list the copy does not hold is refused.**
  Such a change could neither be checked nor undone, so nothing is written and the reply starts
  with "Error:".
- **Changed: alarm_text delete_entries refuses to remove every entry of a text list.** A text-list
  import leaves a list without entries in the file unchanged, so that request could never land; it
  is now refused before anything is written (the reply starts with "Error:"). Keep at least one
  entry, or use delete_textlist.
- import_textlists says in its reply what it checked: with overwrite=true every list the file
  names carries the file's definitions and entries and every other list is unchanged; with
  overwrite=false every new list carries the file's definitions and entries and every existing
  list is unchanged. create_textlist on a device with no user text list names the helper list it
  imported if that list could not be removed afterwards.

## v3.44.40 (18-10-2026)
- **Fixed: technology object writes that had no effect were reported as warnings, not errors.**
  technology_objects rename, delete and export now reply "Error: ..." when the read-back shows
  the action did not happen (the name is unchanged, the object is still listed, no file was
  written). Before, these replies started with "WARNING:" and were counted as successful calls.
  A rename that landed under a different name, or whose result cannot be read back, is also
  reported as an error and says so.
- **Fixed: technology_objects set_parameter replies now match what the read-back shows.** A
  value that was accepted but not applied replies "Error: ... the write was accepted and
  discarded"; a write whose result cannot be read back replies "Error: ... NOT verified"; a
  value that ends up different from the one requested is an error too. Writing the value the
  parameter already holds is a success: "... unchanged (already <value>)".
- **Fixed: technology_objects import of a file that creates nothing looked like a success.** It
  now replies "Error: technology_objects import of '<file>' imported no objects." instead of
  "Imported 0 technology object(s)".
- **Fixed: export_all_xml led with success lines when some blocks failed.** If any block cannot
  be exported, the reply now starts with "Error: export_all partially failed - <n> of <m> blocks
  exported", lists the failed blocks first, then the exported ones. When an export produced no
  file, the reply says the existing file was not changed only if there was one.
- **Fixed: library delete_unused_types could report invented counts.** If the check after the
  cleanup fails, the reply now starts with "Error: library cleanup ran but the result could not
  be reconciled (<reason>) - removed/remaining counts unknown" and lists every error the check
  collected. Before, it listed a made-up "still present" entry and computed removed/remaining
  counts from it.

## v3.44.39 (18-10-2026)
- **Internal: test hooks for technology object and library cleanup replies.** No change in
  behaviour. Test-only switches let the test build make a technology object delete, export or
  parameter write have no effect (or make its read-back fail), and make the library cleanup
  check fail, so the next release can be checked against the replies those cases produce. The
  switches are off in every normal installation.

## v3.44.38 (18-10-2026)
- **Internal: test hook for text-list writes.** No change in behaviour. A test-only switch
  lets the test build fail a text-list write (create_textlist, add_entries, delete_entries,
  update_entries and import_textlists) right after the new text lists were imported, so the
  next release can be checked against what such a write leaves behind when it fails at that
  point. The switch is off in every normal installation.

## v3.44.37 (18-10-2026)
- **Fixed: watch import_table with overwrite=true could replace a watch table with no copy to go back to.**
  The table is now exported first. If that copy cannot be taken or read back, the import is refused and the
  reply says "no backup could be taken ... NO changes were applied". After the import the table is read back
  and compared with the file, row by row (address, display format, modify value, triggers, force value and
  the comment in every language; two rows with the same address are both kept). The row check accepts the
  forms in which TIA stores an entry: a tag name written without quotes (Clock_1Hz) is stored with quotes
  ("Clock_1Hz"), an absolute address that belongs to exactly one tag (%M60.0) is stored as that tag's name,
  and the display format Date is stored as DATE_AND_TIME. An address that two tags share, or that no tag
  owns, is compared exactly as written. The reply says "verified" only when the table holds exactly the
  file's rows. When it does not, the table is put back from the copy, checked again, and the reply starts
  with "Error:" and says the table was RESTORED; if even that fails, it says the table could NOT be restored
  and names the copy ("Backup KEPT at: <path>").
- **Changed: watch import_table takes exactly one watch table per call.** A file holding several tables
  (or a force table) is refused before anything is written; so is a table name that matches more than one
  table, a table name that belongs to a force table, and overwrite=false for a table that already exists.
  A new table is still created at the top of the watch tables tree, and its rows are checked the same way.
- **Fixed: tag import_table with overwrite=true could run without a backup.** When the copy of the existing
  table cannot be taken or read back, the import is now refused before anything is written ("no backup
  could be taken ... NO changes were applied") instead of running with the problem noted only in the server
  log. When the import fails, or the table cannot be found afterwards, the table is put back from the copy
  and the reply says RESTORED only after the restored table was read back and matched the copy; otherwise
  it names the copy ("Backup KEPT at: <path>"). An unused copy is no longer left in the temp folder.
- **Changed: when watch import_table or tag import_table stops on an unexpected error, the reply says what
  happened to the project** - nothing changed, a new table of that name now exists (or does not), or an
  import had started (and, for a tag table, where the copy of the table is kept).

## v3.44.36 (18-10-2026)
- **Fixed: session save_as saved the original project first.** save_as used to save the open
  project before writing the copy, so unsaved changes ended up in the original as well. save_as
  now writes only the copy: your unsaved changes go into the copy, and the original project's
  data stays as it was on disk. The reply names the project that is open afterwards (the copy)
  and says "original project data unchanged". TIA still removes its own session files (such as
  the lock and index files) from the original folder, as it does whenever a project is closed.

## v3.44.35 (18-10-2026)
- **Internal: test hooks for watch and tag table imports.** No change in behaviour. A test-only switch
  lets the test build fail a watch import_table or a tag import_table right after the table was
  written, so the next release can be checked against what an import leaves behind when it fails
  at that point. The switch is off in every normal installation.

## v3.44.34 (18-10-2026)
- **Fixed: udt export_xml replaced an existing file without being asked.** With an outputPath that
  already exists, export_xml now refuses ("... already exists - pass overwrite=true to replace it.
  NO changes were applied.") unless you pass the new overwrite=true. Default: false.
- **Fixed: a failed export could delete the file it was meant to replace.** udt export_xml,
  library export_type_xml and blocks_read export_all_xml now write each export to a temporary
  file next to the target ("<name>.tmp-<8 hex>.xml"), check that it exists and is not empty, and
  only then swap it in. If the export fails, the existing file stays exactly as it was and the
  temporary file is removed. export_all_xml now also replaces an existing block file in the
  output folder instead of failing on it.
- **Fixed: export_all_xml could report success lines first when it stopped part-way.** If the
  whole export stops, the reply now starts with "Error:" and gives how many blocks had been
  exported and how many had failed.

## v3.44.33 (18-10-2026)
- **Internal: test hook for exports.** No change in behaviour. A test-only switch lets the
  test build fail a library type export and an export_all block export right after the
  export call returned, so the next release can be checked against what an export leaves
  behind when it fails at that point. The switch is off in every normal installation.

## v3.44.32 (18-10-2026)
- **Fixed: a failed DB member edit could leave the DB changed, with no copy to go back to.**
  db add_member, delete_member, update_member, update_member_comment and set_start_value
  (single and bulk) now keep a copy of the DB taken before the edit, read the DB back after
  the edit and compile it. The reply ends with "(verified: compiled)" only when the DB holds
  exactly the requested change and every other member is unchanged (names, data types, start
  values including array elements, retain setting, access flags and the comment in every
  language). When the edit did not land as requested, the DB is put back from that copy,
  checked again, and the reply starts with "Error:" and says the DB was RESTORED. If even
  that fails, the reply says the DB could NOT be restored and names the file that holds the
  copy ("Backup KEPT at: <path>"). A know-how protected DB is refused before anything is
  written. set_start_value no longer tells you to compile to verify. A line break in a member
  comment is now stored as a space - TIA keeps a member comment on one line - and the reply says so.

## v3.44.31 (18-10-2026)
- **Internal: test hook for DB member edits.** No change in behaviour. A test-only switch
  lets the test build fail a db member edit right after the new DB content was written, so
  the next release can be checked against what an edit leaves behind when it fails at that
  point. The switch is off in every normal installation.

## v3.44.30 (18-10-2026)
- **Internal: test hook for the save_as investigation.** No change in behaviour. A test-only
  switch lets the test build run save_as without first saving the original project, so the
  next release can be checked before save_as stops saving the original. The switch is off in
  every normal installation.

## v3.44.29 (18-10-2026)
- **Fixed: udt add_member refused new members as "already exists".** A name such as
  SetPoint or None, or the name of a member inside a Struct, was refused by add_member
  although the UDT had no top-level member of that name, and the single and the bulk
  add_member could disagree about it. Both now refuse only a name that a top-level member
  already uses; the refusal still says "already exists".

## v3.44.28 (18-10-2026)
- **Fixed: udt get_structure missed or flattened nested members.** For a UDT with Struct
  members, get_structure left out members inside a Struct or listed them as if they were
  top-level members, could show a child's comment on its Struct, and printed a wrong member
  count. Nested members are now listed under their Struct by path (for example
  "1.1 AxisX.Home : Real"), each comment is shown on the member that owns it, in the
  project's editing language, and the summary reads
  "Total members: <n> top-level (<m> declared)". Members that come from a UDT type used
  inside the UDT are not listed one by one.

## v3.44.27 (17-10-2026)
- **Fixed: a UDT with Struct members could not be edited.** Editing any member of a UDT
  that holds a Struct (or an Array of Struct) was refused by TIA, because the rebuilt type
  lost its nesting. The rebuild now keeps every Struct with its members, so such a UDT can
  be edited like any other.
- **Fixed: a start value could land on the wrong member.** A Struct member could take the
  start value of one of its own members. A Struct member now keeps only its own start value.
- **Array element start values are kept.** The start values of single array elements (for
  example Arr[0] := 7), in an array or in an Array of Struct, now survive an edit of any other
  member. If they cannot be put back, the edit is undone and the reply says why.
- **Fixed: a '|' or a line break in a member comment changed the UDT.** A comment such as
  "range 0|100" turned its second half into a start value, and a line break could add a
  member nobody asked for. Comments are now stored exactly as given (line breaks and other
  control characters become spaces in the generated source only), in udt create, add_member
  and update_member_comment alike.
- **Nested members can be addressed by their path.** delete_member, update_member_comment
  and update_member accept a dotted path such as AxisX.Home. A top-level member with that
  exact name is matched first; a bare name with no top-level match picks the single nested
  member of that name; a name that matches several nested members is refused and the reply
  lists their paths. A path that two members share (possible when a member name itself
  contains a '.'), or a dotted path through such a member, is refused and the reply shows
  the members involved. In a bulk delete_member every name is checked against the UDT as it
  was before the call, so an ambiguous name is reported instead of silently picking the
  remaining match. add_member still adds top-level members only.
- **Safer member input.** A member name with a line break, ';' or '"', or a dataType with a
  line break, ';', '//' or '(*' that is not an inline 'Struct ... END_STRUCT' block, is
  refused before anything changes ("NO changes were applied"). So is a start value with a
  line break, or with ';', '//' or '(*' outside a quoted string. Quoted strings may use the
  '$' escape ('It$'s'). Inline Struct dataTypes keep working as before.
- **Changed: a bulk delete_member that would empty the UDT (or a Struct in it) is refused
  as a whole** before anything changes, instead of deleting the other members.

## v3.44.26 (17-10-2026)
- **Fixed: a UDT member edit lost the comments of other languages.** In a project with
  more than one language, editing one member (comment, type, start value, add or delete)
  kept only one language of every member's comment and could overwrite the editing
  language with another language's text. Every language of every member is now carried
  over, the caller's comment goes into the project's editing language exactly as given
  (after line breaks become spaces), and the result is read back: if a comment did not
  land, the edit is rolled back and the reply names it. A comment inside a member whose
  type the edit changed is not copied onto the new type. When the editing language cannot
  be read, the reply says where the caller's text was written ("NOTE: editing language
  unreadable - comments carried for every culture, caller text written to <culture>").

## v3.44.25 (17-10-2026)
- **Fixed: a tag comment write could damage the tag table with no way back.** add_tag (with a
  comment, including bulk add_tag) and tag update_comment replace the whole tag table to set one comment. They now
  save a copy of the table first and check the table afterwards: the comment must be there
  and every other tag (comments in every language, addresses, access settings) must be
  unchanged. If the write or the check fails, the table is put back from the copy and the
  reply says so. If even that fails, the reply says the table could not be restored and names
  the file that holds the copy. When nothing was written (for example a comment with a
  character TIA cannot store), update_comment says no changes were applied; add_tag says the
  tag was created but its comment was not.

## v3.44.24 (17-10-2026)
- **Fixed: a UDT member edit could move an attribute to the wrong member.** When two
  members had the same name at different depths (for example AxisX.Home and AxisY.Home),
  one of them lost its ExternalVisible / ExternalWritable / ExternalAccessible / SetPoint
  value, and a value could be copied onto a member at another depth. Every value now goes
  back to exactly the member it came from, a start value set on a member inside a
  UDT-typed member is kept too, and the edit is read back: if a value did not land, the
  edit is rolled back and the reply names it ("AxisX.Home.SetPoint expected true, read false").
- **Fixed: create_block overwrite could reset member and block attributes and still report
  success.** The block is now exported before it is replaced. If the attributes cannot be
  carried over and read back, the whole overwrite is rolled back from that export and the
  reply says "ROLLED BACK"; if even that fails, the reply says so and names the kept
  export. When the export cannot be taken (an inconsistent block), the overwrite still runs
  and the reply opens with "WARNING: no backup could be taken - no rollback possible".
  A failed overwrite that changed the block names the kept export ("Pre-overwrite export
  KEPT at: ...").

## v3.44.23 (17-10-2026)
- Internal: test support for an upcoming fix to tag comment writes. No change in behaviour.

## v3.44.22 (17-10-2026)
- Internal: test support for upcoming fixes to create_block overwrite. No change in behaviour.

## v3.44.21 (17-10-2026)
- **Fixed: the force table export left the TagComment column empty.** Force table entries
  are stored with peripheral access ("tag":P), and the export did not match that name to
  its tag, so no row ever showed the tag's comment. The export now matches it.

## v3.44.20 (17-10-2026)
- **Fixed: tag comments were read wrongly.** get_table_details, tag search, the watch and
  force table exports and export_io_map showed a tag's comment under the wrong name or not
  at all: a comment could appear on a tag named like its table, a tag whose name contains
  '&' lost its comment, and '&', '<' and '>' in a comment were shown escaped ('&amp;').
  All five now read the same way: the comment of each tag, under its real name, with the
  text as typed. User constants are never shown as tag comments.
- **Fixed: tag comments were shown in the wrong language.** In a project whose editing
  language is not English, the readers showed the English comment. They now show the
  comment in the project's editing language, and fall back to the first language that has
  a comment only when that one is empty.

## v3.44.19 (17-10-2026)
- **Fixed: a UDT member edit could report success while it lost data or changed nothing.**
  add_member, delete_member, update_member_comment, update_member and the bulk modes now
  take a backup of the UDT first, compile the rebuilt type, read it back and compare it
  with the edit that was asked for. Only then does the reply say success, and it says
  where the type is: "(verified: compiled, at 'Folder/')". If anything does not match, the
  original UDT is restored from the backup and the reply says so ("was NOT applied ...
  RESTORED from the pre-edit backup"). If TIA refuses the generated source, its error text
  is quoted in full and the reply says NO changes were applied.
- **Fixed: member attributes were lost on a UDT in a folder.** ExternalVisible,
  ExternalWritable, ExternalAccessible and SetPoint were silently reset by an edit of a UDT
  that sits in a folder. They are now carried back into the UDT's own folder; if that
  fails, the edit is rolled back instead of reporting success.
- **Fixed: `udt create` with members and targetFolder put the UDT at the container root.**
  The new type is now compiled before it is moved, so it lands in the requested folder. If
  it cannot be placed there, or does not verify, it is removed and the reply says why; the
  generated external source is kept so the errors can be read.
- A UDT member comment with line breaks is stored on one line (TIA keeps a member comment
  on one line); each line break becomes a space and the reply says so.
- Bulk delete_member no longer re-adds the last member as a Bool when every member was
  named; that member is reported as not deleted and is left unchanged.
- Known limit until a later release: a UDT with Struct members, or with comments in more
  than one language, cannot be edited yet - the edit is refused with the original intact.

## v3.44.18 (17-10-2026)
- **Fixed: a tag comment could land on the wrong tag.** tag update_comment and add_tag
  wrote the comment to the first tag of the table when a tag had the same name as its
  table, and could not comment a tag whose name contains '&' ("could not find tag").
  The comment now always lands on the named tag, and every other tag is left as it was.
- **Fixed: tag comments were written in the wrong language.** In a project whose editing
  language is not English, update_comment and add_tag overwrote the English comment.
  The comment now goes into the project's editing language; the other languages are kept.
- Tag comments with line breaks are stored on one line (TIA keeps a tag comment on one
  line); each line break becomes a space and the reply says so.
- A tag comment TIA cannot store (for example a control character) is refused before
  anything is imported; update_comment then says NO changes were applied.

## v3.44.17 (17-10-2026)
- Internal: test support for upcoming fixes to UDT member edits. No change in behaviour.

## v3.44.16 (17-10-2026)
- Internal: test support for upcoming fixes to UDT member edits. No change in behaviour.

## v3.44.15 (17-10-2026)
- Internal: groundwork for upcoming fixes to UDT member edits and tag comments. No change in behaviour.

## v3.44.14 (16-10-2026)
- **Fixed: LAD and FBD OBs could not be created on some S7-1200 CPUs.** create_ob with
  language=LAD or FBD failed with TIA's "Section 'Input' is not valid for this block"
  (measured on an S7-1200 FW V3.0; after the fix create_ob LAD / FBD / SCL, also as OB30
  CyclicInterrupt, all land there; the create_block LAD/FBD OB route is guarded by the
  same retry). When TIA gives exactly that answer, the
  OB's Input section (Initial_Call / Remanence) is now removed and the import is retried
  once; the reply notes that the section was dropped. CPUs that accept the section, such
  as the S7-1500, still get it. If the retry fails too, the reply shows TIA's message and
  nothing is left behind.

## v3.44.13 (16-10-2026)
- **Fixed: OB numbers TIA accepts were refused.** create_block refused every OB number
  from 2 to 122 ("reserved for system OBs"), although TIA accepts for example OB30/OB31
  as cyclic interrupts or OB100 as startup. That rule is gone: the number is sent to TIA
  together with the OB type, and TIA decides. When TIA refuses, the reply names the OB
  number, the type, the device and TIA's own message, and nothing is left behind.
  Without a number, OBs are still numbered from 123 upward. When TIA rejects an OB import
  over its AutoNumber setting (as it does for time-error and diagnostic-error OBs), the
  import is retried once without that setting. When an OB's own code does not compile,
  the reply says so and shows the compile messages instead of blaming the number.
- **Fixed: create_ob ignored obNumber and the OB type.** create_ob obNumber=30 created
  OB123 and reported success. create_ob now honours obNumber, accepts a new secondaryType
  argument (for example CyclicInterrupt), and its reply states the number and type the
  OB really has. create_fb / create_fc also honour fbNumber / fcNumber on the default
  SCL route.
- **Fixed: rename_block with an invalid newNumber renamed the block anyway.** newNumber is
  now checked before anything changes, and a number TIA refuses is reported as an error
  that says whether the rename itself was applied.

## v3.44.12 (15-10-2026)
- **Fixed: the multi-client confirmation phrase could authorise deleting a folder's
  contents.** delete_folder with deleteContents=true accepted any text containing
  "I understand", including the phrase used to confirm actions while several AI clients
  are connected. Deleting a non-empty folder now needs the exact phrase
  "I understand this deletes the folder contents"; deleting a tag that has a comment or
  access flags needs "I understand this destroys the comment and access flags". Other
  confirmation checks no longer accept the multi-client phrase either.

## v3.44.11 (15-10-2026)
- **Fixed: publishing to a library with onCollision=replace could lose the existing master
  copy.** The old copy was deleted first, so a failed publish left none. Now the old copy is
  kept under a temporary name until the new one is created and checked, and is put back if
  anything fails.
- **Fixed: a folder-qualified block or UDT name (e.g. "Motors/Valve") did not detect an
  existing master copy.** replace silently created a second copy named "<name>_1", and
  onCollision=fail did not refuse. Both now match the existing copy by its real name.

## v3.44.10 (15-10-2026)
- Internal: test instrumentation for an upcoming library publish (replace) safety fix. No change in behaviour.

## v3.44.9 (15-10-2026)
- **Fixed: moving a tag to another tag table could lose it.** If adding the tag to the target
  table failed after it had been removed from the source, the tag ended up in neither table.
  Now the source table is backed up first, the moved tag is checked in the target (type,
  address, comment, access flags), and any failure restores the source table from the backup.

## v3.44.8 (15-10-2026)
- Internal: test instrumentation for an upcoming tag-move safety fix. No change in behaviour.

## v3.44.7 (15-10-2026)
- **Fixed: moving a UDT, tag table or watch table between folders could lose it.** If the
  move failed after the original was removed, the restore could fail too and the item was
  gone. Now the target folder is tested with a temporary copy first (UDTs and watch tables),
  and a move that fails at the last step restores the original from an untouched backup.
  A moved UDT is also compiled, so it can be exported again right away.

## v3.44.6 (15-10-2026)
- Internal: test instrumentation for an upcoming folder-move safety fix (UDTs, tag tables, watch tables). No change in behaviour.

## v3.44.5 (14-10-2026)
- **Fixed: moving a block could lose it.** A know-how protected block was deleted and could
  not be brought back, and a move whose import failed could leave the block missing. Now a
  protected block is refused before anything changes (move it in the TIA Portal UI instead),
  the target folder is tested with a temporary copy before the original is touched, and a
  move that fails at the last step restores the original block from an untouched backup.

## v3.44.4 (14-10-2026)
- Internal: test instrumentation for an upcoming block-move safety fix. No change in behaviour.

## v3.44.3 (14-10-2026)
- **Fixed: overwriting an SCL block with code that does not compile replaced the working
  block.** The tool reported success while the original code was gone, and an overwrite
  that asked for a block number already in use could silently change the number. Now, when
  the new code does not compile (or the number cannot be applied), the original block is
  restored with its original number and the reply is an error that lists the compiler
  messages. Creating a NEW block with code that does not compile still works as before.
- **Fixed: a failed SCL block creation could leave an empty block reported as success.**
  The silent fallback is gone; the real generation error is returned instead.

## v3.44.2 (14-10-2026)
- **Fixed: a failed block import could lose the original block.** When an overwrite import
  failed partway (or the imported block did not verify), the original was not always put
  back, and the backup copy could be removed even though the reply said it was kept. The
  original is now restored whenever a backup exists, the backup is only removed after a
  verified result, and a failed import is reported as an error that states exactly what
  is left.

## v3.44.1 (14-10-2026)
- Internal: test instrumentation for upcoming data-safety fixes. No change in behaviour.

## v3.44.0 (20-09-2026)
- **The tool surface follows your licence live.** When your licence changes (you
  re-validate or log out in the Manager, or your account's plan changes), the available
  tools update in the SAME session -- no client restart. A licence losing access drops
  the affected tools immediately.
- **Logout button in the Manager.** Next to Re-validate: confirms, wipes the licence data
  on this machine, and returns to the registration screen so a different user can activate.
  The seat itself is not released server-side in this version.
- **Fixed: the registration screen could come up blank.** After a licence wipe (revoked
  licence, or the new Logout) the License tab showed an empty page instead of the
  registration form. It now always shows the form.
- Internal diagnostics improvements for the beta-tester program.

## v3.43.0 (20-09-2026)
- **The tools now reload without restarting your AI client when a stale licence refreshes.**
  Before, if TiaCommander started with an expired local licence cache (the common case -- a
  fresh session after the 72-hour cache lapses), you had to open the Manager, re-validate, AND
  fully restart Claude Desktop / your client before the tools came back. Re-validating in the
  Manager alone did not bring them back. Now the server re-checks the licence with the licence
  API on the first 'get_info', and if it is valid it reloads the full tool set in the SAME
  session automatically -- no Manager step, no restart.
- **A call made while unlicensed now says the tools are reloading, not "restart your client".**
  If the client asks for a licensed tool during a refresh, it gets a "refreshing the licence --
  the tools will reload in a moment, please retry shortly" message. The retry succeeds once the
  notification has reloaded the surface.
- Under the hood: one shared, testable licence-validation core reused by the Manager's
  re-validate and activate paths and by the new server-side self-heal; the heal is
  single-flight with a cooldown (it never hammers the licence API) and never blocks tool calls;
  the licence state transition is synchronised so the reload always sees the licensed state.
- No change to any tool's behaviour or arguments. Deployment is a separate operator step.

## v3.42.0 (07-09-2026)
- **Compiler results now list only real diagnostics.** TIA propagates an error flag up the
  whole tree with empty text on every intermediate level, and the tools used to print all of
  it -- 14 "error" rows for 7 actual errors, with rows for the device, the folder, and the
  summary line presented as if they were errors. Every compile-reporting action (blocks_read,
  blocks_write auto-compile, hardware, technology_objects) now shows one row per actual
  diagnostic, and the totals always match the compiler's own count. Any disagreement between
  the listing and the count is printed, never hidden.
- **The full message tree is still available.** get_compiler_messages renders every node,
  hierarchy visible, nothing suppressed -- and the filtered views name that route in their
  own output.

## v3.41.4 (07-09-2026)
- **The stored-credential list now says whether each one is for a project or global.** Two
  credentials for the same PLC used to look identical, so deciding which to delete meant
  guessing. Each row now states its scope, names the project it belongs to, and marks the one
  that applies to the project currently open in TIA.

## v3.41.3 (07-09-2026)
- **The Credentials tab no longer writes progress messages to the log.** The diagnostics added
  in v3.41.1 to track down the Add-dialog crash are switched off now that it is fixed. They can
  be turned back on by setting `TIACOMMANDER_LOG_DEBUG=1`, should it ever be needed again.
- Genuine failures are still logged, as before.

## v3.41.2 (07-09-2026)
- **The Credentials tab now says what the passwords are for** -- they are used by the live_data
  tools when reading from a PLC.
- **It also states what decides what.** Whether a password is needed comes from the CPU's
  configured protection level, not its firmware version. Firmware decides the transport:
  S7-1200 V4.5+ and S7-1500 V2.9+ connect over TLS, older CPUs connect in plaintext, and
  `live_data connect` reports which was actually negotiated for the CPU in front of you.

## v3.41.1 (06-09-2026)
- **Fixed: adding a PLC password closed the Manager.** Clicking "+ Add" on a project row in
  the Credentials tab terminated the application instead of opening the dialog. The dialog now
  opens, and can be cancelled and reopened normally.
- The Add path now writes progress to the log, so if it ever fails again the log says exactly
  where. Passwords are never written to the log.

## v3.41.0 (06-09-2026)
- **PLC passwords can now be stored per project, not just globally.** A project's own
  credential is tried first and a global one is used only as a fallback, so two machines
  that share an IP no longer have to share a password.
- **A new Credentials tab in the Manager** lists your projects and the PLC each one talks
  to, shows which project is currently open in TIA Portal, and lets you add, view, replace
  or delete a password without leaving the app. Archived copies are left out of the list,
  and a Modified column tells apart several copies of the same project.
- **Connecting to a PLC now explains what to do when it fails.** Instead of a generic
  driver error, the reply says whether a password is needed, which stored credential was
  used, or that a stored password was created on a different machine and must be
  re-entered here.
- A CPU that is read by absolute address is no longer told it is password-protected.
- **Fixed: a failed installation reported itself as a PLC problem.** An incomplete update
  produced an error that looked like the controller had refused the connection. It is now
  reported as what it is, and the installer no longer leaves the files half-updated.
- **Fixed: the Manager could close itself when the Credentials tab was used.** It appeared
  to freeze and then vanish without a message.

## v3.40.0 (30-08-2026)
- **New `technology_objects` tool.** Fifteen actions covering listing and reading technology
  objects, changing parameters, structural edits, export and import, and connecting an object
  to its hardware.
- `get_info` was under-reporting the available surface, listing one tool fewer than actually
  shipped.
- **Known issue found while testing:** compiling can silently downgrade a technology object's
  version even when you did not touch it. Check the version of an axis after a compile.

## v3.39.4 (26-08-2026)
- **Fixed: calling a block whose input is itself a function block failed.** The parameter list
  was built with duplicated entries, and TIA rejected the call with "the parameter list of the
  call ... cannot be used". This affected both creating blocks and inserting rungs; only rung
  insertion had been reported.

## v3.39.3 (26-08-2026)
- **Fixed: adding a network to a LAD block was refused if the block also contained SCL.** The
  block's language was judged from one part of it rather than the block itself, so a LAD block
  that happened to hold an SCL section was wrongly treated as SCL. Genuinely SCL blocks are
  still refused, as before.

## v3.39.2 (25-08-2026)
- **Import actions now say up front that they replace the whole object.** Anything absent from
  the file you supply is reset rather than left alone. This was already true; now
  `udt import_xml`, `watch import_table` and both `blocks_write` import routes tell you so
  before you run them.

## v3.39.1 (25-08-2026)
- **Fixed: a block's title, comment and header family/name were emptied by an overwrite that
  never mentioned them.** They now survive, and there is a way to read them back so they can
  be resupplied deliberately.

## v3.39.0 (25-08-2026)
- **Fixed: overwriting a block cleared block attributes you had set.** Settings such as the IEC
  check flag were reset by an overwrite that never mentioned them. Attributes that older CPUs
  reject are still left out on purpose.

## v3.38.9 (25-08-2026)
- **Fixed: an organization block asked for as a cyclic interrupt came back as a program cycle.**
  It compiled and reported success, so the wrong block type was easy to miss. Overwriting an OB
  without naming its type no longer reclassifies it either.

## v3.38.8 (25-08-2026)
- **Fixed: member attributes were lost when a data type was rebuilt.** The four boolean member
  attributes are now captured before the rebuild and restored afterwards.

## v3.38.7 (25-08-2026)
- **Fixed: editing one UDT member reset the attributes of other members.** Updating a comment on
  one member could clear another member's visibility, write protection and setpoint flags. Because
  a UDT is a type, every data block and tag created from it inherited the change, including
  re-enabling an HMI/OPC-UA write path that had been deliberately closed.

## v3.38.6 (25-08-2026)
- **Fixed: overwriting a block moved it out of its folder.** Creating a block in a target folder
  always worked; it was the overwrite that dropped the placement.
- Clarifies the previous release's note, which had wrongly reported creation as broken too.

## v3.38.5 (25-08-2026)
- **Fixed: overwriting a block silently discarded its number, memory layout and author.** A block
  created as FC211 / Standard / with an author came back as FC9 / Optimized / no author when
  overwritten without those arguments. All three now carry through.

## v3.38.4 (25-08-2026)
- **Fixed: overwriting a watch table created a duplicate instead.** The existing table in a folder
  was never found, so an overwrite added a second table at the root under a modified name and left
  the original in place, after which a later edit could reach the wrong one.

## v3.38.3 (25-08-2026)
- **Errors about a wrong argument type now name the argument.** Passing a number where text was
  expected produced a raw .NET sentence that never said which argument was at fault.
- Passing `null` for an argument now uses that argument's documented default instead of behaving
  as if nothing was supplied.

## v3.38.2 (25-08-2026)
- **Fixed: `library get_tree` and `library find` ignored `depth`, `limit` and `typeFilter` when
  they were supplied as text.** The result was the opposite of the request in a reply that looked
  entirely successful: asking for six items could render thirty-five, because a dropped depth
  means unlimited.

## v3.38.1 (25-08-2026)
- **Fixed: exporting block XML inline while the device was online failed with "Parse Error: Data
  at the root level is invalid".** The real reason was replaced by a message about the error
  message. This was the fourth export path with the same fault; the other three were fixed
  earlier.

## v3.38.0 (24-08-2026)
- **New: `delete_scl_statement` removes a single SCL statement from a network**, instead of
  requiring a whole network to be rewritten by hand.
- **New: `split_network` divides one network into several.** Both were previously either
  impossible or too risky to attempt against a live plant.

## v3.37.0 (24-08-2026)
- **Every block overwrite is now transactional.** The block is exported before the import and
  restored automatically if the result does not compile, so a failed edit no longer leaves a
  block inconsistent, a state that disables every other network-editing tool.
- **New `get_edit_capabilities`** reports what can actually be edited for a given block.

## v3.36.0 (21-08-2026)
- **`create_block` now says which interface attributes the chosen route cannot carry.** Supplying
  a member remanence on an SCL-based create silently dropped it and reported success. The note
  names only what you actually supplied.

## v3.35.0 (21-08-2026)
- **A pin can now be left unwired**, by passing `null` for it in `pinWires`. Both obvious
  spellings were previously rejected even though the underlying format supported it.

## v3.34.0 (21-08-2026)
- **`force=true` no longer lets an unusable operand through.** Forcing a dotted operand that
  cannot resolve used to leave the block inconsistent with no repair path but deleting it.
  `force` still does what it is for: accepting a single tag name that does not exist yet.

## v3.33.0 (21-08-2026)
- **A tag name containing a dot now explains how to write it.** Such tags always worked when
  quoted; unquoted they were split and reported as undefined, and the refusal suggested a typo,
  which led to the capability being reported as missing entirely.

## v3.32.0 (21-08-2026)
- **Fixed a regression present since v3.24.0:** `blocks_read export_xml_file`,
  `blocks_read export_source` and `udt export_xml` refused the documented inline route, demanding
  an `outputPath` they never read.

## v3.31.0 (21-08-2026)
- **Fixed: archiving a project or library could report "no file at expected path" for a file that
  was still being written.** The result is now awaited briefly rather than checked once.

## v3.30.0 (21-08-2026)
- **An online-mode failure is no longer blamed on inconsistent blocks.** Every export error had a
  "compile first" remedy appended, including errors that said in as many words that the device was
  online. The message now names the device and tells you to go offline.

## v3.29.0 (21-08-2026)
- **A compile with warnings but no errors is no longer reported as a failure.** It now reads as
  success and names the warning count, rather than hiding it.

## v3.28.0 (21-08-2026)
- **Live data: a symbol that appears on the PLC after a download is now found without
  reconnecting.** The symbol list was read once per connection, so anything downloaded afterwards
  stayed invisible. A miss now refreshes the list once and retries.

## v3.27.1 (21-08-2026)
- **Fixed `udt create` with `members[]`, `targetFolder` and `createParents`**, which v3.27.0
  shipped broken on every path: supplying members failed outright, and the folder arguments were
  accepted and silently ignored.

## v3.27.0 (21-08-2026)
- `udt create` accepts `members[]` plus `targetFolder`/`createParents`.
- **Superseded by v3.27.1**: as shipped, this did not work on any path. Use v3.27.1 or later.

## v3.26.0 (19-08-2026)
- **A UDT member called `name` is no longer silently lost.** Adding one reported success and
  then wasn't there. Reserved words are now quoted, as they already were for FB/FC/OB members.
- `udt create` now says up front that it leaves a `_placeholder` member behind, instead of
  only mentioning it afterwards.

## v3.25.0 (19-08-2026)
- **A single TRUE/FALSE value no longer makes a whole network read-only.** Networks containing
  a Bool constant written by TIA itself reported as not editable, which switched off rung
  editing for everything else in that network. They now read and edit normally.
- Values TIA does not produce are still refused rather than guessed at, so a malformed
  constant is reported instead of being silently reinterpreted.

## v3.24.0 (19-08-2026)
- **Leaving out a required argument now tells you which one.** Previously the error named the
  tool, pointed you at the schema, and appended an internal .NET message ("The given key was
  not present in the dictionary"). It now says exactly which argument is missing and lists
  what the action accepts.
- Calls that supply everything are unaffected, and actions the check does not cover behave
  exactly as before.

## v3.23.0 (19-08-2026)
- **`create_block` can now create a block directly inside a folder** -- `targetFolder` and
  `createParents`, the same arguments the other creation actions already take. Previously the
  arguments were accepted and quietly ignored, and the block appeared at the root.
- SCL blocks are the exception, and the tool now says so rather than silently rooting them:
  they are created first and then moved, and moving requires the whole project to compile
  cleanly. If the move cannot happen you are told the block exists, where it is, and what to
  run next.
- **Block numbers are no longer lost when creating an SCL block.** Asking for a specific
  number gave you a different one; the number is now applied and verified, and overwriting a
  block keeps its original number.
- **Three actions stopped reporting success after a failed import.** `replace_network`,
  `update_network_element` and rung edits (`update_rung`/`insert_rung`/`populate_network`)
  each checked nothing before printing a success line -- rung edits even embedded the failure
  text inside it.

## v3.22.0 (18-08-2026)
- **Master copies, library types and library folders can now be renamed** --
  `library action=rename_master_copy`, `rename_type` and `rename_library_folder`. Renaming
  a master copy does **not** rename the block or UDT inside it, and the result now says so
  explicitly rather than leaving you to discover it.
- Every rename reads the name back from the library before reporting success, so a rename
  that silently did not take is reported as a failure instead of a success.
- A `newName` containing `/` or `\` is refused. Names are addressed through
  slash-separated paths, so a name containing one produces an item that cannot be
  referenced afterwards.
- `folders action=rename_folder` on technology objects was already working; only its
  description said otherwise, which v3.21.0 corrected.

## v3.21.0 (18-08-2026)
- **Library types that wrap a UDT can now be instantiated.** Asking for one used to be
  refused outright, with a message saying the capability was coming in a future update.
  It was not coming, and it was not needed -- the UDT simply had to be created in *PLC
  data types* rather than in *Program blocks*. Instantiating a master copy that wraps a
  UDT works for the same reason; if neither route accepts it, both failures are reported
  rather than one.
- **Library folders can be created in the Types section.** `create_library_folder` and
  `delete_library_folder` take `typeFilter=types|master_copies`. Every folder previously
  landed under *Master copies*, with no way to say otherwise.
- **The `delete_unused_types` preview now depends on the mode you asked for.** Both modes
  used to print the same report, so "will be deleted" appeared next to types that
  `preserve_default` keeps. The preview also gained a third outcome -- *likely skipped* --
  for types nothing instantiates but something in the library still holds, and it now
  lists the evidence each verdict rests on. Running the cleanup for real reports which of
  its predictions actually happened.
- **Global libraries can be cleaned up.** They used to be refused with "only available on
  the project library", and the workaround that refusal suggested could not work. Removing
  a whole type is still unavailable there and now says why; read-only and system libraries
  are refused for what they are.
- **`get_master_copy` returns a content list instead of a promise.** It used to say an XML
  body would arrive in a later update. No such export exists for master copies, so it now
  lists what the copy contains and states that master copies carry no dependencies --
  publish referenced blocks and UDTs separately.
- **A block version of `1.2.3` is refused instead of silently discarded.** It previously
  reported success and left the block with no version at all. Block versions are
  major.minor; library type versions are a different field and can have three parts.
- **New: `library action=reload_global_library`.** Re-reads a global library changed
  outside the session. It refuses while there are unsaved changes unless you say to
  discard them.
- `folders action=rename_folder` no longer claims renaming is impossible everywhere. It
  works on technology objects; the message names the five containers where it does not.

## v3.20.0 (18-08-2026)
- The device name shown by `session list_devices` is now accepted everywhere. Previously
  only the exact full name worked -- the shorter station name and the CPU name were
  rejected as "not found", even though the tools display them.
- When a shorter name is used, the result says which device it resolved to, so you are
  never left guessing which PLC a command acted on. If a short name matches more than one
  device, the call is refused and lists the candidates rather than picking one.
- A device name that does not exist now lists the names that do, instead of only saying it
  was not found -- and on the download path it no longer fails with an internal error.

## v3.19.5 (18-08-2026)
- Internal only: test records and verification. No user-visible change.

## v3.19.4 (18-08-2026)
- FIX (could close the wrong program): `session disconnect` with `closeTia` closed
  whichever TIA Portal the API happened to list first, which is not necessarily the one
  your session was using. With two TIA Portals open it could close a colleague's -- losing
  their unsaved work. TiaCommander now remembers which instance it attached to and closes
  that one, and the result names the process it closed.
- If TiaCommander cannot tell which instance is yours and more than one is running, it now
  refuses and closes nothing, rather than guessing. Reconnect with `session action=connect`
  so the instance is recorded, or close TIA Portal yourself.
- This was reachable only from v3.19.0, where `closeTia` given "true" or 1 started working.

## v3.19.3 (18-08-2026)
- Internal only: verification. No user-visible change.
- Confirms that a tag's data type and address are stored exactly as supplied and are not
  validated when set; a bad value is reported by the compiler, not at the time of writing.

## v3.19.2 (18-08-2026)
- Internal only: verification. No user-visible change.
- Worth knowing: the live-data connection belongs to the session that opened it, so connecting
  from one client does not make the connection available to another.

## v3.19.1 (18-08-2026)
- **Arguments that were accepted and then ignored now warn.** `add_interface_member` and
  `update_interface_member` declared parameters their handlers never read, and that declaration
  suppressed the very warning that would have told you the argument had no effect.

## v3.19.0 (18-08-2026)
- IMPORTANT: `session disconnect` with `closeTia` given as "true" or 1 previously did
  NOTHING -- TIA Portal stayed open holding your project. It now closes TIA Portal, which
  is what the parameter always said it would do. If you have been passing closeTia in that
  form and relying on TIA staying open, this will change what happens.
  Known limitation, being tracked separately: when several TIA Portal instances are
  running, this closes the first one the API reports, which may not be the one your
  session is attached to.
- FIX (wrong PLC): `download_upload upload_station` with `addressIndex` given as a string
  such as "2" silently used index 0 instead -- uploading a DIFFERENT physical PLC into
  your project. `upload_check` then echoed your own value back, so there was no way to see
  the substitution. Such a call is now refused before anything is uploaded.
- FIX: `folders`, `watch`, `alarm_text`, `live_data`, `hardware` and `blocks_write`
  arguments given the wrong kind of JSON value are no longer silently ignored. Where the
  intent is unambiguous the value is accepted and the result tells you it was adjusted
  (a number written as a string, a single item where a list was expected, a boolean
  written as "true" or 1); otherwise the call is refused and names the parameter.
- FIX (silent inversion): network flags such as `useRouter` given any string other than
  "true" -- for example "yes" or "1" -- were written as FALSE without a word. They are
  now either applied correctly or refused.
- Note on scope: this release converts the arguments covered by the tracked defect list.
  A number of other string arguments still behave as before.

## v3.18.9 (18-08-2026)
- **Fixed: a start value or comment given as a number or `true`/`false` was skipped, while the
  result still reported the other changes it made**, a partial success that looked like a full
  one. It is now refused and names the parameter.
- **Fixed: hardware flags given as `0`/`1` had no effect** and the call failed complaining about
  a missing parameter instead. Worse, any other spelling such as `yes` or `True ` with a trailing
  space was written as **false** without comment. All are now handled or refused by name.

## v3.18.8 (18-08-2026)
- **Fixed: `tags` or `members` given as a single string polled the entire tag table or data block
  instead of the item you asked for.** A bare string is now treated as a one-element list, with a
  note saying so.

## v3.18.7 (18-08-2026)
- **Fixed: a single alarm entry supplied as one object created a text list with no entries at
  all**, and the success message could not reveal it because its count came from the same empty
  result.
- **Fixed: `textListNames` given as a bare string exported every text list**, which sat in front
  of a destructive round trip.

## v3.18.6 (18-08-2026)
- **Fixed: a watch modify or force value given as a number or boolean was silently skipped and
  the old value survived.** It is now refused, naming the parameter.

## v3.18.4 (18-08-2026)
- **`depth` and `limit` given as numeric text now work** in `folders get_tree` and `folders find`,
  with a note; genuinely invalid values are refused by name instead of producing a raw parser
  error.
- **Fixed: `typeFilter` given as an array was quietly turned into "all"**, so the filter you asked
  for was never applied.

## v3.18.3 (18-08-2026)
- Internal only: shared argument-handling foundation for the fixes in the releases that follow.
  No user-visible change.

## v3.18.2 (18-08-2026)
- FIX: creating a block with a `Constant` interface section works again. It previously
  failed the import outright with "The attribute 'Remanence' cannot be set.", so the whole
  block was rejected -- not just the constant. This is the known issue listed under v3.18.1.
- A `Constant` member is a compile-time literal, so remanence does not apply to it and TIA
  refuses the attribute. It is no longer emitted for that section. `Accessibility` is still
  emitted, which is what TIA itself writes for constants.
- No other section changes: `Input`, `Output`, `InOut` and `Static` members still carry
  their remanence exactly as before, including an explicit `remanence` you pass in.

## v3.18.1 (18-08-2026)
- CHANGE: every interface member must now have a `name` and a `type`. Previously a member
  missing its name was created as a variable literally called `unnamed`, and one missing
  its type silently became a `Bool` -- neither of which you asked for, and both of which
  compiled cleanly.
- CHANGE: an unrecognised member key, or a section name that is not one of the six, is now
  refused with the list of accepted names instead of being ignored. The message points out
  the common mix-up: `create_block` calls the data-type key `type`, while
  `add_interface_member` and `db.add_member` call it `dataType`.
- These are the changes in this release most likely to reject a call that previously
  appeared to work, which is why they are separate from v3.18.0. Section names remain
  case-insensitive and a JSON `null` still means "not supplied".
- Known issue, not introduced here: creating a block with a `Constant` section fails with
  "The attribute 'Remanence' cannot be set." It is being tracked separately.

## v3.18.0 (18-08-2026)
- FIX (data loss, could destroy a live block): when you create a block, a section of the
  `interface` given an object instead of an array silently lost every member in it. With
  `overwrite=true` that replaced a LIVE function block's interface with an empty one, and
  every instance data block lost its members with it. The call reported success and the
  block compiled with 0 errors and 0 warnings. Such a call is now refused before anything
  reaches TIA Portal.
- FIX: `interface` given a string, or an array instead of an object, was ignored entirely
  and the block was created with no interface at all -- again including when overwriting
  an existing one.
- FIX: a member's `startValue`, `comment`, `remanence` or `accessibility` given a number
  or a boolean was dropped without a word, so a declared default that seeds every
  instance data block simply disappeared. These are now refused, naming the parameter.
  A start value is always a string: "33", "TRUE", "T#2S", "16#00FF" -- TiaCommander will
  not guess which you meant from the JSON type.
- FIX: a section that TIA Portal cannot hold on that kind of block -- `Static` on an FC,
  or `Input` on an OB -- was discarded in silence. It is now refused by name, and the
  message lists the sections that block type does accept. The documentation advertised
  all six sections for every block type; it has been corrected.
- Unchanged on purpose, so working calls keep working: section names are still matched
  regardless of capitalisation, a JSON `null` still means "not supplied", an empty
  section is still allowed, and a correctly formed interface behaves exactly as before.

## v3.17.3 (18-08-2026)
- CHANGE: a rung key that TiaCommander does not recognise is now refused, and the message
  lists the keys it does accept. Previously a misspelling such as `contactss` or
  `paralellBranches` was accepted and quietly ignored, so the block was built without
  that part of your logic and still compiled cleanly. A key that differs only in
  capitalisation is now named as such ("keys are case-sensitive, use 'contacts'").
- This is the one change in this release that can reject a call that used to work. Four
  things are deliberately still accepted: the `uids` block returned by `get_network`
  (so read-modify-write round trips keep working), the keys of a `{type:'call', ...}`
  rung, the keys of the rung-level `instruction` shorthand, and the older `name` / `tag`
  / `bitTag` spellings inside contact and coil objects.

## v3.17.2 (18-08-2026)
- FIX (silent wrong behaviour): a LAD rung key given the wrong kind of JSON value was
  ignored instead of refused, and the block still compiled with 0 errors and 0 warnings.
  Three of these changed what the PLC would do:
  - `contact` given an array dropped EVERY condition. Through `update_rung` that left
    the coil connected straight to the powerrail -- an output that is permanently ON --
    and the call reported success.
  - a `parallelBranches` entry given a plain string deleted the whole OR branch, so
    seal-in and manual-override logic disappeared.
  - `negated` given the string "true" produced a normally-open contact instead of a
    normally-closed one, inverting an interlock.
- Forgiving where it is safe, strict where it is not. A shape that can be corrected
  without guessing is now accepted AND reported back to you: `contact` given an array
  becomes `contacts`, `output` given a plain operand string becomes a coil, `negated`
  accepts true/false, "true"/"false" and 0/1. A shape that would require guessing is
  refused with the exact JSON path -- `parallelBranches` is never guessed, because
  ["#a","#b"] could mean two branches of one contact or one branch of two.
- `output` and `coil` in the same rung is now reported as a conflict. Previously `coil`
  quietly won and one of your two instructions was discarded.
- A coil with nothing driving it is now refused with an explanation, instead of failing
  with an internal TIA message quoting an element id that appears nowhere in your input.
- Wrong-shaped values inside call rungs, boxes and coil objects are refused by name
  rather than replaced with an empty string.

## v3.17.1 (17-08-2026)
- FIX (data loss): renaming a watch table could destroy it. If TIA rejected the new
  name, the old table had already been deleted and was not put back. The result then
  told you the original XML was preserved at a temp path -- and that file was deleted
  immediately afterwards, so there was nothing to recover from. A failed rename now
  RESTORES the table with its entries intact and says so. If a restore is ever
  impossible, the backup file is kept, not deleted, and the message names it.
- Note: TIA accepts more characters in a table name than you might expect (a slash, an
  angle bracket, a trailing dot and very long names are all fine); a double quote is
  rejected. Previously a rejected name cost you the table.

## v3.17.0 (17-08-2026)
- NEW: tag action=import_table imports a tag table from raw TIA XML -- the same
  document export_tag_table_data format=xml produces. Accepts a file path, inline
  XML, or an export ID straight from the export database. Requires offline mode.
  With overwrite=true the existing table is backed up first and restored if the
  import fails, and the result reports the measured tag count before and after
  plus every tag that was dropped, so you are never asked to take the import's
  word for what it did.
- tag action=export_tag_table_data now registers every export in the export
  database under the name of the table, so admin action=list_exports shows it and
  get_export retrieves it. Previously an export wrote a file and list_exports
  answered "No exports stored".
- admin action=list_exports gained a Subject column naming what each export is
  an export OF. Until now two exports of two different tables looked identical.
- tag action=export_tag_table_data gained returnInline, which returns the content
  instead of a path: XML and CSV as text, XLSX as base64.
- FIX: exporting the same tag table twice within one second failed with "the
  export cannot be made because the file already exists". The auto-generated name
  is stamped to whole seconds, so the second export collided with the first. It is
  now given a _2 suffix instead, and both files survive.
- NEW: tag action=rename, which always refuses and explains why. TIA Openness
  cannot rename a PLC tag, and it is not faked by deleting and re-adding: tag
  names are global to the controller and nothing cascades, so the program would
  keep referencing the old name with no way to repair SCL networks. Rename in the
  TIA Portal IDE, which does cascade.
- xref action=find_callers now documents what it could already do: it accepts a
  PLC TAG name as well as a block name, and answers which blocks read or write
  that tag, with Read/Write and the network. When a name matches no block and no
  tag it now says so, instead of reporting that nothing references it.

## v3.16.0 (17-08-2026)
- NEW: tag action=move relocates a single tag between tag tables and keeps
  everything -- its comment and all three access flags (accessible, visible,
  writable). Until now there was no way to move one tag, so the only route was
  delete plus add, and that silently reset all four. The move verifies by reading
  the values back afterwards and reports a failure if anything did not survive.
- tag action=delete_tag now REFUSES when the tag carries a comment or non-default
  access flags, because add_tag cannot put them back. It names what would be lost
  and points at action=move. Pass confirm to delete anyway. This also applies when
  deleting several tags at once.
- tag action=search now shows the Comment column, and prints "(none)" when a tag
  genuinely has no comment. Previously comments were invisible here while other
  actions showed them, so an empty result could not be told apart from a hidden
  field.
- The M/I/Q assignment list bit map was drawn in the opposite order to its own
  column header, so every byte read mirror-imaged. Bit 0 now appears under the
  "0" column.

## v3.15.3 (17-08-2026)
- Live data: when a symbol cannot be found, the message now says it is not in the
  cached symbol set and suggests reconnecting, instead of stating that the symbol
  is not declared in the PLC. The symbol list is captured once when the connection
  is made, so after a download the tool could report a symbol as absent when it
  exists on the PLC. The wording no longer claims to know something it cannot.
- Documentation: corrected several statements about what the engineering API
  supports. Notably, creating a data type inside a folder is only restricted for a
  brand-new type -- one created from a library type or master copy lands in the
  target folder directly. Folder rename limitations were also overstated.

## v3.15.2 (02-08-2026)
- The published tool descriptions no longer carry internal research references. The text an AI client reads is tidied up; no tool, action or argument changed -- descriptions only.
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
- Note: there is still no subscription and no push. A live subscription was measured to be incompatible with ordinary reads on the same connection, so it is not offered.

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
- New: an internal activity timeline of every tool call the server made, newest first, with time, tool, action, device, duration, success and error. It records only what TiaCommander did; actions you perform by hand in TIA Portal are not visible to any interface and do not appear.
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
- Groundwork for running several AI clients against TiaCommander at once (first phase; not yet user-visible). Internal transport proven and the response path refactored behind a per-client output abstraction, with no change to how the current single client is served -- verified byte-for-byte identical.
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

## v2.30.0 (11-06-2026)
- Internal only: finer-grained error reporting in usage telemetry. No user-visible change.

## v2.29.0 (10-06-2026)
- Improved: Degraded mode now distinguishes between "TIA Portal not installed" and "TIA Portal installed but Openness API not enabled", providing tailored setup instructions for each case
- Improved: Degraded mode detects and displays the installed TIA Portal version (e.g. V19) even when Openness is missing
- Improved: TiaCommander Manager GUI now launches normally when TIA Portal is not installed (previously blocked with an error dialog), allowing license registration before TIA Portal setup
- Improved: Restart instructions in degraded mode guide users to use Ctrl+Alt+Delete and Task Manager for a clean restart
- Added: Telemetry now tracks degraded mode sessions with reason codes for better install-experience analytics

## v2.28.0 (10-06-2026)
- Added: `hardware action=dump_catalog` -- dumps the full TIA Portal hardware catalog (~11,000 entries) to local database. Auto-detects TIA Portal version changes and re-dumps on upgrade
- Added: `hardware action=search_catalog` -- live real-time search of TIA Portal hardware catalog (requires TIA Portal running)
- Added: `admin action=search_device_catalog` -- offline search of locally cached hardware catalog by article number, type name, or description. No TIA Portal required
- Added: `admin action=reset_device_catalog` -- clears local catalog cache and triggers re-dump on next TIA Portal connection
- Added: Device catalog telemetry -- hardware catalog entries are included in telemetry data for aggregated device usage insights
- Added: Per-project device catalog collection -- walks project DeviceItems recursively to catalog all installed hardware modules

## v2.27.0 (10-06-2026)
- Added: `session action=get_state` -- proactive state check returning connection level (0-4), TIA/project/device status, license info, and restart flag. Call at session start for instant situational awareness
- Added: Graceful state guards across all tools -- instead of cryptic exceptions when TIA Portal is not connected or no project is open, tools now return clear guidance on what to do next
- Added: Restart detection -- if your license is activated or changes mid-session, every tool response includes a restart reminder so the AI assistant picks up the new tool set
- Improved: `session action=info` now includes state level and guidance for non-ready states
- Improved: Server instructions updated to recommend `get_state` as the first call in every workflow

## v2.26.3 (07-06-2026)
- Added: MCPB bundle (.mcpb) in GitHub Releases -- install TiaCommander in Claude Desktop with one click (Settings -> Extensions -> Advanced Settings -> Install Extension)
- Improved: Manager terminal now shows restart instructions after license activation
- Fixed: auto-update now preserves manifest.json and icon.png in MCPB extension directory

## v2.26.1 (26-05-2026)
- Fixed: `open_manager` failing on fresh downloads with "The operation was canceled by the user" -- Windows SmartScreen blocks shell-executed processes from internet-downloaded EXEs in headless mode
- Fixed: SQLite database file leaking into release ZIP
- Fixed: release notes encoding for non-ASCII characters (em dashes)

## v2.26.0 (26-05-2026)
- **Breaking:** Siemens Openness DLLs (`Siemens.Engineering.dll`, `Siemens.Engineering.Hmi.dll`) are no longer included in the download. TiaCommander now loads them directly from your TIA Portal installation at runtime.
- **Requirement:** TIA Portal V15.1 or later must be installed on your machine. TiaCommander will not start without it.
- New: runtime assembly resolver automatically discovers your TIA Portal installation via Windows registry, with filesystem fallback
- New: startup log shows which TIA Portal installation was found and how it was discovered
- Improved: release security gate now checks for accidental inclusion of proprietary DLLs
- Retracted download assets from v2.14.1, v2.25.0, v2.25.5 -- these releases contained proprietary Siemens DLLs that should not have been redistributed

## v2.25.5 (22-05-2026)
- New: User Guide (`docs/USER_GUIDE.md`) -- covers Manager commands, auto-update workflow, and general usage
- Updated CHANGELOG for v2.25.1-v2.25.4

## v2.25.4 (22-05-2026)
- Fixed: self-update no longer aborts when the AI assistant keeps a headless MCP server process running -- the updater now force-kills remaining processes after a 10-second grace period instead of aborting
- Improved: update confirmation prompt now shows numbered steps explaining what will happen (download, close, replace, reopen, restart AI assistant)
- Self-update pipeline tested end-to-end: v2.25.1 -> v2.25.4 via GitHub Releases

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
- New: `export_hardware_map` action -- exports raw Siemens CAx/AML hardware data as a flat table (single sheet XLSX or CSV)
- New filename convention for hardware exports: `{ProjectName}_{PLCName}_{ExportType}_{timestamp}.{ext}`
- Tag names and comments are cross-referenced for every I/O address in the pinout

## v2.23.0 (16-05-2026)
- New: `export_io_map` action on the hardware tool -- exports a complete device I/O map combining rack/slot topology with tag assignments
- XLSX output includes one sheet per hardware slot plus a summary sheet with DI/DO/AI/AO/HSC totals
- CSV output provides a flat combined view with all slots in one file
- Supports single-device or all-device export (one file per device)
- Every physical address is listed -- unassigned addresses show blank tag name/comment

## v2.22.0 (16-05-2026)
- New: `open_file` action on the admin tool -- opens any exported file using the Windows default application (Excel for .xlsx, browser for .html, etc.)
- Improved: all export actions now prompt to open the exported file after completion

## v2.21.0 (16-05-2026)
- Fixed: stale TIA Portal handle no longer causes crashes when switching between launch/connect/create operations. Session create is now fully self-contained -- auto-launches or attaches to TIA Portal and creates the project in one call.
- Fixed: existing block numbers are preserved when updating blocks with overwrite. No more unintended OB number increments.
- Improved: watch and force table entries now use data-type-appropriate display formats (Bool, DEC_signed, Float) instead of defaulting to Hex.
- Improved: project create, archive, and library operations now default to configured root paths. No need to specify directories manually if configured via Manager -> Paths tab.

## v2.20.0 (16-05-2026)
- Comprehensive download configuration handling -- 3-tier config handler covers all 30+ Openness download prompts (certificate trust, firmware mismatch, alarm text libraries, etc.) instead of only 7
- Added `pcInterfaceName` parameter to `download_to_device` for explicit network adapter selection on multi-NIC PCs (VPN + WiFi scenarios)
- Hardware downloads now auto-stop PLC modules (required for hardware configuration loading)
- Firmware mismatch guidance in tool description -- directs users to Online & Diagnostics to check actual PLC firmware version
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

- Block number auto-assignment: fbNumber, fcNumber, obNumber, blockNumber parameters now clearly documented as optional with auto-increment. AI agents no longer need to specify block numbers -- the server assigns the next sequential number automatically.

## v2.18.1 (16-05-2026)

- Added LAD instruction catalog to blocks_write tool description: all 50 instruction types with pin names, data types, and usage notes
- Fixed NORMALIZE instruction: default DestType changed from Int to Real (was causing import errors with Real inputs)
- Fixed DECO/ENCO instructions: added required DisabledENO attribute and correct version numbers (V4.5+ firmware only)
- Verified JUMP, JUMPNOT, SWITCH, JMPLIST instructions with cross-network label wiring on S7-1200 V3.0

## v2.18.0 (15-05-2026)
- **New: export your data to CSV or Excel.** Four new actions: `tag export_tag_table_data`,
  `watch export_watch_data`, `watch export_force_data` and `alarm_text export_alarm_data`, with
  auto-generated filenames.
- **New default exports folder**, configurable in the Manager and reported by `get_info`.
- Tag comments are included in exports, and watch and force lists cross-reference their comments
  from the tag tables.

## v2.17.3 (15-05-2026)

- Fixed LAD block import on S7-1200 V3.0 firmware: removed DisabledENO attribute from 13 instruction types (MOVE, AND, OR, XOR, INV, SHL, SHR, RL, RR, DECO, ENCO, SEL, CONVERT, ROUND, CEIL, FLOOR, TRUNC, SCALE_X, NORMALIZE, MOD, NEG, ABS, EXPT, CALC)
- Fixed R_TRIG/F_TRIG chaining: eno output pin now properly connects to the next element's en input
- Fixed FB/FC Call rung parsing: now accepts both flat format (`{type:"call", blockName:...}`) and nested format (`{call:{blockName:...}}`)
- Improved tool descriptions: documented instance DB requirements for R_TRIG, F_TRIG, TON, TOF, TP, CTU, CTD, CTUD; documented LAD rung formats in networks parameter

## v2.17.2 (15-05-2026)
- Hardware tool: added guidance for new-project PLC setup workflow (network scan -> identify by MAC address -> assign IPs -> configure connection -> download)
- Hardware tool: added V4.5+ firmware note -- directs user to set protection level when compile fails with "Password must not be empty"

## v2.17.1 (15-05-2026)
- `save_as` no longer requires an explicit directory -- defaults to the configured projects root when `newParentDirectory` is omitted (consistent with `create` and `archive`)

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
- New action: `session action=list_archives` -- list all `.zap*` archive files from configured or explicit archives root
- Shows filename, size (MB), last modified date, and TIA Portal version per archive
- Archives sorted newest-first; scans 1 level deep with 2-level fallback
- `get_info` now shows archive count when archives root is configured
- Optional `archivesRoot` parameter to scan a custom directory

## v2.16.0 (13-05-2026)
- Configurable default folders: set a TIA Portal Projects Folder and Archives Folder so you don't need to type full paths every time
- New GUI folder pickers on the MCP Configuration tab in TiaCommander Manager
- `session action=configure` -- set `projectsRoot` and `archivesRoot` via MCP (validates directory exists)
- `session action=create` -- `parentDirectory` is now optional when a projects root is configured
- `session action=open` -- omit `projectPath` to list all available projects from the configured root (scans 2 levels deep)
- `session action=archive` -- `outputDirectory` is now optional when an archives root is configured
- `get_info` now shows configured projects and archives root paths
- Fixed: Show Terminal checkbox state now persists correctly across restarts

## v2.15.0 (12-05-2026)
- New action: `hardware -> set_network_config` -- configure IP address, subnet mask, default gateway, PROFINET device name, IP protocol selection, ISO protocol, and station name on project devices
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
- Terminal output sanitized -- no sensitive data displayed
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
- TIA Portal V15.1-V19 compatibility

## v0.1 (25-10-2025)
- Initial release: MCP server architecture (JSON-RPC 2.0 over stdio)
- TIA Portal Openness API integration
