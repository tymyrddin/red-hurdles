# Identify critical information

To identify critical information, the red team needs to use an adversarial approach and ask themselves what 
information an adversary, the blue team in this case, would want to know about the mission. If obtained, the 
adversary will be in a solid position to thwart the red team's attacks. 

Critical information is not necessarily sensitive information, but is any information that might jeopardise the 
red teams plans if leaked to an adversary. Examples:

* Client information that the team has learned. It is unacceptable to share client specific information such as 
people's names, roles, and infrastructure that the team has discovered. Sharing this type of information should be 
kept on need-to-know basis as it could compromise the integrity of the operation. The Principle of Least Privilege 
(PoLP) dictates that any entity (user or process) must be able to access only the information necessary to carry 
out its task. PoLP should be applied in every step taken by the Red Team.
* Red team information, such as identities, activities, plans, capabilities and limitations. The adversary can use 
such information to be better prepared to face the attacks.
* Tactics, Techniques, and Procedures (TTP) that the team uses in order to emulate an attack.
* OS, cloud hosting provider, or C2 framework used by the team. If the team uses Pentoo for penetration testing, 
and the defender knows this, they can keep an eye for logs exposing the OS as Pentoo. Depending on the target, 
there is a possibility that other attackers are also using Pentoo to launch their attacks; but, there is no reason 
to expose the OS if you do not have to.
* Public IP addresses that the red team will use. If the blue team gains access to this kind of information, they 
could quickly mitigate the attack by blocking all inbound and outbound traffic to these IP addresses.
* Domain names that the team has registered. Domain names play a significant role in attacks such as phishing. 
If the blue team figures out the domain names the team will be using to launch their attacks, they could block or 
sinkhole the domains to neutralise the attack.
* Hosted websites, such as phishing websites, for adversary emulation.