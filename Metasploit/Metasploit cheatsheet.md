# Metasploit For Beginners : Youtube course


## Part 1
#### Metasploit interfaces :
1. msfconsole
2. armitage (GUI)
3. msfcli

- start postgresql service : (makes msfconsole faster)
```shell-session
# service postgresql start
```
- then start msfconsole :
```shell-session 
# msfconsole
```
- to show documentation/help :
```shell-session
msf > help
```
- to use an exploit (example :adobe_flash_avm2):
this vulnerability is already patched 
```shell-session
msf > use windows/browser/adobe_flash_avm2
```
- show information about an exploit :
```shell-session
msf exploit(windows/browser/adobe_flash_avm2) > show
```
- show options/payloads/targets : 
```shell-session
msf exploit(windows/browser/adobe_flash_avm2) > show <options/targets/payloads>
```
- search 
```shell-session
search <name/type/...>
```

## Part 2
- metasploit module types :
1. exploit
2. post
3. auxiliary
4. payloads
5. encoders
6. NOPs