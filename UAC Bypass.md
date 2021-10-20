## Bypass UAC (User Account Control) without dropping a DLL file in the system

<br>
---> <span style='color:red;'>I haven't tried this method yet</span> <---

[Source](https://enigma0x3.net/2016/08/15/fileless-uac-bypass-using-eventvwr-exe-and-registry-hijacking/)

[Script](https://github.com/enigma0x3/Misc-PowerShell-Stuff/blob/master/Invoke-EventVwrBypass.ps1)

- To execute a script when "running unsigned scripts" is disabled on the machine, you can either ==sign your script with a self-signed certificate== , ==disable this security measure== using the command ``` set-executionpolicy remotesigned ``` (**requires admin privilege**), or use the command :
```powershell
powershell.exe -noprofile -executionpolicy bypass -file .\script.ps1
```

The malicious script used to bypass UAC can also be detected by windows defender !!
(McAfee doesn't detect though !!!!!!!)