# Assess risks

In OPSEC, risk assessment requires learning the possibility of an event taking place along with the expected cost 
of that event. Thus, this involves assessing the adversary's ability to exploit the vulnerabilities.

Once the level of risk is determined, countermeasures can be considered to mitigate that risk:

* The efficiency of the countermeasure in reducing the risk.
* The cost of the countermeasure compared to the impact of the vulnerability being exploited.
* The possibility that the countermeasure can reveal information to the adversary.

Example: We can expect that a SIEM would make it reasonably uncomplicated to detect suspicious activity and connect 
three events such as scanning with Nmap, sending various phishing emails, and using the Metasploit framework from the 
same machine. The related risk is high. But if we know that the adversary has minimal resources for detecting 
security events, we can assess the risk related to this vulnerability as low.
