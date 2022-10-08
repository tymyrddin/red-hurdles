# SNMP

If SNMP is available, it can hold a trove of information. A simple tool is `snmp-check`. On Kali, it can be found in the 
`/usr/bin/snmp-check/` directory. 

```text
$ snmp-check -h
snmp-check v1.9 - SNMP enumerator
Copyright (c) 2005-2015 by Matteo Cantoni (www.nothink.org)

 Usage: snmp-check [OPTIONS] <target IP address>

  -p --port        : SNMP port. Default port is 161;
  -c --community   : SNMP community. Default is public;
  -v --version     : SNMP version (1,2c). Default is 1;

  -w --write       : detect write access (separate action by enumeration);

  -d --disable_tcp : disable TCP connections enumeration!
  -t --timeout     : timeout in seconds. Default is 5;
  -r --retries     : request retries. Default is 1; 
  -i --info        : show script version;
  -h --help        : show help menu;
```

For installing `snmpcheck` on a local Linux box, you need `ruby` installed, and:

```text
git clone https://gitlab.com/kalilinux/packages/snmpcheck.git
cd snmpcheck/
gem install snmp
chmod +x snmpcheck-1.9.rb
```