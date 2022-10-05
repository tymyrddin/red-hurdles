# Analyse vulnerabilities

In a context like red teaming, an OPSEC vulnerability exists when an adversary can obtain critical information, 
analyse the findings, and act in a way that would affect the red team's plans.

Example: You use Nmap to discover live hosts on a target subnet and find open ports on live hosts. Moreover, you 
send various phishing emails leading the victim to a phishing webpage you're hosting. Furthermore, you're using 
the Metasploit framework to attempt to exploit certain software vulnerabilities. These are three separate activities, 
but if you use the same IP address(es) to carry out these different activities, this would lead to an OPSEC 
vulnerability. 

Example: An unsecured database that is used to store data received from phishing victims. If the database is not 
properly secured, it may lead to a malicious third party compromising the operation and could result in data being 
exfiltrated and used in an attack against the client's network. Instead of helping the client secure their network, 
you end up helping a third party expose login names and passwords.