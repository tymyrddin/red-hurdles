# Linux

## System

Linux distribution and release version:

    user@red-linux-enumeration:~$ ls /etc/*-release
    /etc/lsb-release  /etc/os-release
    user@red-linux-enumeration:~$ cat /etc/os-release
    NAME="Ubuntu"
    VERSION="20.04.4 LTS (Focal Fossa)"
    ID=ubuntu
    ID_LIKE=debian
    PRETTY_NAME="Ubuntu 20.04.4 LTS"
    VERSION_ID="20.04"
    HOME_URL="https://www.ubuntu.com/"
    SUPPORT_URL="https://help.ubuntu.com/"
    BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
    PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
    VERSION_CODENAME=focal
    UBUNTU_CODENAME=focal

Hostname:

    hostname

Useful for password cracking (later) are `/etc/passwd`, `/etc/group`, and `/etc/shadow`. Any user can read the 
`passwd` and `group` files. The `shadow` password file requires root privileges. It contains the 
hashed passwords. Cracking the hashes, the original passwords are known.

```text
$ cat /etc/passwd                                                                               
$ cat /etc/group                                                                              
$ sudo cat /etc/shadow          
```

To find the installed applications:

    ls -lh /usr/bin/
    ls -lh /sbin/

On an RPM-based Linux system, get a list of all installed packages with: 

    rpm -qa

On a Debian-based Linux system, get the list of installed packages with:

    dpkg -l

## Users

Files such as `/etc/passwd` reveal usernames, and various commands can provide more information and insights 
about other users on the system and their whereabouts.

Who is logged in:

    who

Current user effective (invoking) user id:

    whoami

`w` shows who is logged in and what they are doing:

    w

To print the real and effective (invoking) user and group IDS:

    id

The allowed command for the invoking user on the current system:

    sudo -l

A listing of the last logged-in users; who logged out and how long they stayed connected:

    user@red-linux-enumeration:~$ last
    user     pts/0        10.9.1.191       Sat Oct  8 17:57   still logged in
    reboot   system boot  5.4.0-120-generi Sat Oct  8 17:45   still running
    reboot   system boot  5.4.0-120-generi Mon Jun 20 13:10 - 13:13  (00:02)
    randa    pts/0        10.20.30.1       Mon Jun 20 11:00 - 11:01  (00:00)
    reboot   system boot  5.4.0-120-generi Mon Jun 20 09:58 - 11:01  (01:03)

## Networking

IP adresses:

    ip a s

The DNS servers can be found in the `/etc/resolv.conf`.

`netstat` is a command for gathering information on network connections, routing tables, and interface statistics.

    user@red-linux-enumeration:~$ sudo netstat -lvanp -t | grep "LISTEN"
    [sudo] password for user: 
    tcp        0      0 127.0.0.1:953           0.0.0.0:*               LISTEN      615/named           
    tcp        0      0 0.0.0.0:389             0.0.0.0:*               LISTEN      722/slapd           
    tcp        0      0 127.0.0.1:6667          0.0.0.0:*               LISTEN      729/inspircd        
    tcp        0      0 10.10.180.205:53        0.0.0.0:*               LISTEN      615/named           
    tcp        0      0 127.0.0.1:53            0.0.0.0:*               LISTEN      615/named           
    tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      583/systemd-resolve 
    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      687/sshd: /usr/sbin 
    tcp6       0      0 ::1:953                 :::*                    LISTEN      615/named           
    tcp6       0      0 :::389                  :::*                    LISTEN      722/slapd           
    tcp6       0      0 fe80::42:f5ff:fecd:d:53 :::*                    LISTEN      615/named           
    tcp6       0      0 ::1:53                  :::*                    LISTEN      615/named           
    tcp6       0      0 :::21                   :::*                    LISTEN      650/vsftpd          
    tcp6       0      0 :::22                   :::*                    LISTEN      687/sshd: /usr/sbin

`netstat -atupn` will show All TCP and UDP listening and established connections and the program names with addresses 
and ports in numeric format.

List Open Files (IPv4 and IPv6 listening services and ongoing connections):

    user@red-linux-enumeration:~$ sudo lsof -i
    [sudo] password for user:
    COMMAND   PID      USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
    chronyd   640    chrony    5u  IPv4  16945      0t0  UDP localhost:323 
    chronyd   640    chrony    6u  IPv6  16946      0t0  UDP localhost:323 
    sshd      978      root    3u  IPv4  20035      0t0  TCP *:ssh (LISTEN)
    sshd      978      root    4u  IPv6  20058      0t0  TCP *:ssh (LISTEN)
    master   1141      root   13u  IPv4  20665      0t0  TCP localhost:smtp (LISTEN)
    master   1141      root   14u  IPv6  20666      0t0  TCP localhost:smtp (LISTEN)
    dhclient 5638      root    6u  IPv4  47458      0t0  UDP *:bootpc 
    sshd     5693     peter    3u  IPv4  47594      0t0  TCP rpm-red-enum.thm:ssh->10.20.30.113:38822 (ESTABLISHED)
    ...

Limit the output to ports:

    sudo lsof -i :<port-number>

## Running services

In `ps aux`, the `a` and `x` are necessary when using BSD syntax as they lift the "only yourself" and "must have a tty" 
restrictions, and it becomes possible to display all processes. The `u` is for details about the user that has the 
process.

    user@red-linux-enumeration:~$ ps -aux | grep "THM"
    randa        659  0.0  0.0   2608   600 ?        Ss   17:45   0:00 /bin/sh -c /home/randa/THM-24765.sh
    randa        677  0.0  0.3   6892  3204 ?        S    17:45   0:00 /bin/bash /home/randa/THM-24765.sh
    user        1195  0.0  0.0   6432   724 pts/0    S+   18:06   0:00 grep --color=auto THM

Use `ps axjf` to print a process tree. The `f` stands for "forest", and it creates an ASCII art process hierarchy:

    user@red-linux-enumeration:~$ ps axf
       PID TTY      STAT   TIME COMMAND
         2 ?        S      0:00 [kthreadd]
         4 ?        S<     0:00  \_ [kworker/0:0H]
         5 ?        S      0:01  \_ [kworker/u256:0]
    ...
       978 ?        Ss     0:00 /usr/sbin/sshd -D
      5665 ?        Ss     0:00  \_ sshd: peter [priv]
      5693 ?        S      0:00  |   \_ sshd: peter@pts/1
      5694 pts/1    Ss     0:00  |       \_ -bash
      5713 pts/1    S+     0:00  |           \_ vi notes.txt
      5723 ?        Ss     0:00  \_ sshd: jane [priv]
      5727 ?        S      0:00      \_ sshd: jane@pts/0
      5728 pts/0    Ss     0:00          \_ -bash
      7080 pts/0    R+     0:00              \_ ps axf
       979 ?        Ssl    0:12 /usr/bin/python2 -Es /usr/sbin/tuned -l -P
       981 ?        Ssl    0:07 /usr/sbin/rsyslogd -n
      1141 ?        Ss     0:00 /usr/libexec/postfix/master -w
      1147 ?        S      0:00  \_ qmgr -l -t unix -u
      6991 ?        S      0:00  \_ pickup -l -t unix -u
      1371 ?        Ss     0:00 login -- root
      1376 tty1     Ss     0:00  \_ -bash
      1411 tty1     S+     0:00      \_ man man
      1420 tty1     S+     0:00          \_ less -s
    ...

