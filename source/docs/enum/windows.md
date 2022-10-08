# Windows

## System

Get detailed information about the system, such as its build number and installed patches:

    systeminfo

Check installed updates - this information will give an idea of how quickly systems are being patched and updated:

    wmic qfe get Caption, Description

For listing installed apps:

    wmic product get name,version,vendor

## Users

To know who you are: 

    whoami 

To know what you are capable of (privileges you have):

    whoami /priv

Which groups you belong to:

    whoami /groups

View users:

    net user

Discover the available groups if the system is a Windows Domain Controller:

    net group

If the system is NOT a Windows Domain Controller:

    net localgroup

List the users that belong to the local administrators' group:

    net localgroup administrators

To see the local settings on a machine:

    net accounts

If the machine belongs to a domain:

    net accounts /domain
    
This command helps learn about password policy, such as minimum password length, maximum password age, and 
lockout duration.

## Networking

System network configuration:

    ipconfig

For the DNS servers, use all network-related settings:

    ipconfig /all

Use netstat to get information, such as which ports the system is listening on, which connections are active, and 
who is using them. Use the option `-a` to display all listening ports and active connections, `-b` to find the 
binary involved in the connection, `-n` to avoid resolving IP addresses and port numbers, and `-o` to display the 
process ID (PID).

    netstat -abno

    Active Connections
    
      Proto  Local Address          Foreign Address        State           PID
      TCP    0.0.0.0:22             0.0.0.0:0              LISTENING       2016
     [sshd.exe]
      TCP    0.0.0.0:135            0.0.0.0:0              LISTENING       924
      RpcSs
     [svchost.exe]
      TCP    0.0.0.0:445            0.0.0.0:0              LISTENING       4
     Can not obtain ownership information
      TCP    0.0.0.0:3389           0.0.0.0:0              LISTENING       416
      TermService
     [svchost.exe]
    [...]
      TCP    10.20.30.130:22        10.20.30.1:39956       ESTABLISHED     2016
     [sshd.exe]
      TCP    10.20.30.130:22        10.20.30.1:39964       ESTABLISHED     2016
     [sshd.exe]

To discover other systems on the same LAN that recently communicated with the system:

    arp -a
                        
# Running services     

    net start