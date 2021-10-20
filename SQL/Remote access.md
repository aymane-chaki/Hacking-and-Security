- Connect to SQL open port (Microsoft SQL Server) :
```shell
#this command uses the mssqlclient tool provided in the impacket package by SecureAuth Corporation
#make sure to use '/' instead of '\' in the <user_ID>
mssqlclient.py <user_ID>@<remote_address> -windows-auth

#another alternative may be to use the sqlcmd command in linux (didn't work for me though)
```