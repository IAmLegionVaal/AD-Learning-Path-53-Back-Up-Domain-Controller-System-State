# AD Learning Path 53 — Back Up Domain Controller System State

## Objective
Create and verify a system-state backup of a domain controller to separate protected storage and document how it will be restored in an isolated recovery exercise.

## Prerequisites
- Windows Server Backup feature
- Separate backup disk or supported target
- Sufficient free space
- Backup administration rights
- Recovery plan

## Setup
1. Install Windows Server Backup.
2. Confirm the target is not the DC system volume and is not ordinary shared user storage.
3. Record volume capacity, filesystem, and protection controls.
4. Run a system-state backup.
5. List the resulting backup versions and included items.
6. Review Windows Backup and VSS events.
7. Document a restore-test plan; a backup is not considered proven until restoration has been tested.

```powershell
Install-WindowsFeature Windows-Server-Backup
wbadmin.exe start systemstatebackup -backuptarget:E: -quiet
wbadmin.exe get versions -backuptarget:E:
Get-WinEvent -LogName 'Microsoft-Windows-Backup' -MaxEvents 100
```

## Validation
```powershell
wbadmin.exe get versions -backuptarget:E:
# Substitute the returned version identifier:
wbadmin.exe get items -version:<VersionIdentifier> -backuptarget:E:
Get-WindowsFeature Windows-Server-Backup
vssadmin.exe list writers
```

## Evidence
Store backup command output, target details, version identifier, item inventory, backup and VSS events, target-protection notes, restore-test plan, errors/remediation, and final pass/fail status under `evidence/`. Do not publish backup contents.

## Troubleshooting
- Unsupported target: use separate supported storage with sufficient capacity.
- VSS failure: inspect all writers and relevant application/system events.
- Backup cannot be listed: verify the exact target and version identifier.

## Security notes
A DC backup contains the directory database and sensitive secrets. Restrict access, protect it from the domain, encrypt where supported, and maintain offline or isolated copies.

## Cleanup
Retain or securely retire backups according to the recovery plan; do not casually delete the only tested copy.

## References
- Microsoft Learn: AD DS backup and recovery
- Microsoft Learn: `wbadmin start systemstatebackup`

## Next activity
`AD-Learning-Path-54-Restore-a-Deleted-AD-Object`
