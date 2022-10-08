# DNS

If we can get a copy of all the records that a DNS server is responsible for answering, we might discover hosts we 
did not know existed.

One easy way to try DNS zone transfer is via the `dig` command. Depending on the DNS server configuration, DNS 
zone transfer might be restricted. If it is not restricted

    dig -t AXFR DOMAIN_NAME @DNS_SERVER

The `-t AXFR` indicates a zone transfer, and `@` precedes the `DNS_SERVER`  we want to query regarding the records 
related to the specified `DOMAIN_NAME`.