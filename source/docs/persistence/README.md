# Introduction

## What?

Creating alternate ways to regain access to a host without going through the exploitation phase all over again.

## Why?

* Re-exploitation isn't always possible: Some unstable exploits might kill the vulnerable process during exploitation, getting you a single shot at some of them.
* Gaining a foothold is hard to reproduce: For example, if you used a phishing campaign to get your first access, repeating it to regain access to a host is simply too much work. Your second campaign might also not be as effective, leaving you with no access to the network.
* The blue team is after you: Any vulnerability used to gain your first access might be patched if your actions get detected. You are in a race against the clock!

## How?

* [Tampering with unprivileged accounts](tampering.md)
* [Backdooring files](backdoor-files.md)
* [Abusing services](services.md)
* [Abusing scheduled tasks](tasks.md)
* [Logon triggered persistence](logon.md)
* [Backdooring the login screen/RDP](backdoor-login.md)
* [Persisting through existing services](existing.md)

