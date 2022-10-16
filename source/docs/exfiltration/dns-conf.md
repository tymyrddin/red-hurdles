# DNS configurations

Additional domain names and network access:

| Domain Name      | IP Address      | Network Access | 
|:-----------------|:----------------|:---------------|
| attacker.thm.com | `172.20.0.200`  | Net 2          |

Records:

| DNS record       | Type | Value            | 
|:-----------------|:-----|:-----------------|
| attNS.tunnel.com | `A`  | `172.20.0.200`   | 
| att.tunnel.com   | `NS` | attNS.tunnel.com | 