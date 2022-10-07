# Windows Scripting Host (WSH)

Windows scripting host is a built-in Windows administration tool which runs batch files to automate and 
manage tasks within the operating system using VBScript.

## Show a message box

    Dim message
    message = "Hello"
    MsgBox message

run it in cmd: 

    wscript hello.vbs

## Run exe files

Run an exe file with VBScript:

    Set shell = WScript.CreateObject("Wscript.Shell")
    shell.Run("C:\Windows\System32\calc.exe " & WScript.ScriptFullName),0,True

Execute it with `wscript`: 

    wscript c:\path\to\calc.vbs

or `cscript`:  

    cscript.exe c:\path\to\calc.vbs

In case of blacklisting, rename it to, for example, `payload.txt` and run it: 

    wscript /e:VBScript c:\path\to\payload.txt
