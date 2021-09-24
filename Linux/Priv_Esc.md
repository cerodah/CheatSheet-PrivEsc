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
dmesg | greo Linux
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
