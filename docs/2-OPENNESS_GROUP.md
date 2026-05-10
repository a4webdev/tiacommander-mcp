# Siemens TIA Openness Group Setup

TIA Portal requires your Windows user to be a member of the **Siemens TIA Openness** group before any external application (including TiaCommander) can connect via the Openness API.

Some TIA Portal installations also create a **Siemens TIA Engineer** group. If your user is already in the TIA Engineer group, Openness access may work without the separate Openness group. However, we recommend adding your user to both groups to avoid issues.

This is a one-time setup. Most TIA Portal engineers already have this configured.

## Check if you already have access

Open PowerShell and run:

```powershell
whoami /groups | findstr "Siemens TIA"
```

If you see **Siemens TIA Openness** or **Siemens TIA Engineer** in the output, you're likely set up. If neither appears, follow the steps below and **restart your PC**.

## Method 1: PowerShell (recommended)

1. Open **PowerShell as Administrator** (right-click → Run as administrator)

2. Run:
   ```powershell
   Add-LocalGroupMember -Group "Siemens TIA Openness" -Member "$env:USERNAME"
   Add-LocalGroupMember -Group "Siemens TIA Engineer" -Member "$env:USERNAME"
   ```

3. Verify:
   ```powershell
   Get-LocalGroupMember -Group "Siemens TIA Openness"
   Get-LocalGroupMember -Group "Siemens TIA Engineer"
   ```

4. **Restart your PC** (mandatory — see below)

## Method 2: Computer Management (GUI)

1. Right-click **Start** → **Computer Management**
2. Navigate to **Local Users and Groups → Groups**
3. Double-click **Siemens TIA Openness**, click **Add**, type your Windows username, click **Check Names**, then **OK**
4. Repeat for **Siemens TIA Engineer**
5. **Restart your PC** (mandatory — see below)

## Important: restart required

After adding yourself to the group, you **must restart your computer**. Logging out and back in is not sufficient — Windows caches group memberships at boot time.

After restarting, verify with:

```powershell
whoami /groups | findstr "Siemens TIA"
```

## Group not found?

If the "Siemens TIA Openness" group doesn't exist on your machine, TIA Portal may not be installed, or the Openness API component was not selected during installation. Reinstall TIA Portal and ensure the **Openness** option is checked.
