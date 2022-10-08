# SMB

Server Message Block (SMB) is a communication protocol that provides shared access to files and printers. 

Check shared folders using net share:

    net share

Results:

    Share name   Resource                        Remark
    
    -------------------------------------------------------------------------------
    C$           C:\                             Default share
    IPC$                                         Remote IPC
    ADMIN$       C:\Windows                      Remote Admin
    Internal     C:\Internal Files               Internal Documents
    Users        C:\Users
    The command completed successfully.