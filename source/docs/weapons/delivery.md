# Delivery techniques

Delivery techniques have to look professional, legitimate, and convincing to the victim in order to follow through 
with the content.

## Email delivery

Email delivery is a common method to send the payload by sending a phishing email with a link or attachment. The goal 
is to convince the victim to visit a malicious website or download and run the malicious file to gain initial access 
to the victim's network or host.

Red teamers have [their own infrastructure for phishing purposes](red-iac:index). Depending on 
the red team engagement requirement, it requires setting up various options within the email server, including 
DomainKeys Identified Mail (DKIM), Sender Policy Framework (SPF), and DNS Pointer (PTR) record.

Red teamers can also use third-party email services such as Google Gmail, Outlook, Yahoo, and others with good 
reputations.

Another possibility would be to use a compromised email account within an organisation to send phishing emails 
within the organisation or to others. The compromised email could be hacked by phishing or by other techniques 
such as password spraying attacks.

## Web Delivery

Another method is hosting payloads on a web server controlled by the red teamers. The web server has to follow 
the security guidelines such as a clean record and reputation of its domain name and a TLS (Transport Layer Security) 
certificate. 

This method includes other techniques such as social engineering the victim to visit or download the malicious file. 
A URL shortener and other tricks could be helpful when using this method.

In this method, other techniques can be combined and used. The attacker can take advantage of zero-day exploits 
such as exploiting vulnerable software like Java or browsers to use them in phishing emails or web delivery 
techniques to gain access to the victim machine.

## USB Delivery

This method requires the victim to plug in the USB with payload physically. This method could be effective and 
useful at conferences or events where the adversary can distribute the USB.

Organisations may have strong security policies such as disabling USB usage within the organisation environment. 
Not all do.

Common USB attacks used to weaponise USB devices include Rubber Ducky and USBHarpoon, charging USB cable, 
such as O.MG Cable. 