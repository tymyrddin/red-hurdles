# Backdooring the login screen/RDP

With physical access to the machine (or RDP in our case), you can backdoor the login screen to access a terminal 
without having valid credentials for a machine.

## Sticky Keys

When pressing key combinations like `CTRL+ALT+DEL`, you can configure Windows to use sticky keys, which allows 
you to press the buttons of a combination sequentially instead of at the same time. In that sense, if sticky keys 
are active, you could press and release `CTRL`, press and release `ALT` and finally, press and release `DEL` to 
achieve the same effect as pressing the `CTRL+ALT+DEL` combination.

To establish persistence using Sticky Keys, we can abuse a shortcut enabled by default in any Windows installation 
that allows us to activate Sticky Keys by pressing `SHIFT` 5 times after which Windows will execute the binary in 
`C:\Windows\System32\sethc.exe`. 

If we replace the binary with a payload, we can then trigger it with the shortcut. We can even do this from the 
login screen before entering any credentials.

We can replace `sethc.exe` with a copy of `cmd.exe` to spawn a console using the sticky keys shortcut, even from 
the login screen. To overwrite `sethc.exe`, we first need to take ownership of it and grant the current user 
permission to modify it.

    takeown /f c:\Windows\System32\sethc.exe
    icacls C:\Windows\System32\sethc.exe /grant Administrator:F
    copy c:\Windows\System32\cmd.exe C:\Windows\System32\sethc.exe

Lock the current session from the start menu, and press `SHIFT` 5 times to access a terminal with `SYSTEM` privileges 
directly from the login screen.

## Utilman

Utilman is a built-in Windows application used to provide Ease of Access options during the lock screen. When 
clicking the ease of access button on the login screen, it executes `C:\Windows\System32\Utilman.exe` with `SYSTEM` 
privileges. Replacing it with a copy of `cmd.exe`, we can bypass the login screen again.

To replace `utilman.exe`:

    takeown /f c:\Windows\System32\utilman.exe
    icacls C:\Windows\System32\utilman.exe /grant Administrator:F
    copy c:\Windows\System32\cmd.exe C:\Windows\System32\utilman.exe

To trigger the terminal, lock the screen from the start button, and click on the "Ease of Access" button.