# Phishing

## Attack tree

```text
1 Gather information (Reconnaissance) (AND)
2 Clone website with a tool like cURL, Invoke-WebRequest (powershell), or webscrapbook (AND)
3 Add payload (AND)
    3.1 Change login page to a credential stealing script (OR)
    3.2 Implant a malware (Crypted and packed)
2 Use email, sms, phone, to persuade a person to go to the site 
```

## Notes

### Email phishing

An adversary poses as a legitimate and trusted business using a similar look and feel to regular email notifications 
to trick users into clicking on a link that takes them a phony website or access portal designed to look like the 
legitimate company website. Victims are then prompted to enter personal information ranging from login names and 
passwords to social security numbers, birthdates, account numbers, and other highly sensitive data. Email phishing 
is popular because of the easy access to databases containing millions of active email addresses, providing potential 
victims with relatively little risk and almost no cost.

### Spear phishing

Spear phishing refers to a phishing attack that targets a specific person.

In so-called spear phishing an adversary creates an email message that appears to be from a trusted sender, the 
tone of the email way more informal than in a phishing attack, and usually contains some sort of emergency with a 
request for help, in such a way that a quick, instinctive action is required to help the other (before thinking 
can intervene). With personal information shared on social networking sites (first and last name, names of family 
members, and employers), spear-phishing is easier than ever.

### Smishing

Short message service (SMS) phishing, also known as smishing, is a phishing attack conducted through text messaging instead of email.

### Vishing

Vishing refers to phishing attacks that use voice over the phone instead of email.

### Whaling

Whaling refers to a phishing attack that targets the "big fish" of a company, such as the CEO.

The victim is researched more thoroughly with reconnaissance and lured with a 
fake document from, for example, the tax office, and state that the opening of the document requires the 
installation of some “security software”, or something similar.

## Mitigations

* [Windows PC operations security](windows-pc-mitigations:docs/opsec/README)
* [macOS operations security](macos-mitigations:docs/opsec/README)
* [Linux PC operations security](linux-pc-mitigations:docs/opsec/README)
* [Mailserver](mailserver-mitigations:index)