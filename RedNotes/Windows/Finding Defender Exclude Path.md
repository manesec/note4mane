# Finding Defender Exclude Path

Finding Defender’s Exclusions on low-privileged user.

## From event log

```
Get-WinEvent -LogName "Microsoft-Windows-Windows Defender/Operational" -FilterXPath "*[System[(EventID=5007)]]" | Where-Object { $_.Message -like "*Exclusions\Paths*" } | Select-Object -Property TimeCreated, Id, Message | Format-List
```

## Check single folder

In the exclusions paths:

```
PS C:\> & "C:\Program Files\Windows Defender\MpCmdRun.exe" -Scan -ScanType 3 -File "C:\xampp\htdocs\|*"
Scan starting...
Scan finished.
Scanning C:\xampp\htdocs\|* was skipped.
```

Not in the exclusions paths

```
PS C:\> & "C:\Program Files\Windows Defender\MpCmdRun.exe" -Scan -ScanType 3 -File "C:\programdata\|*"
Scan starting...
CmdTool: Failed with hr = 0x80508023. Check C:\Users\svc_web\AppData\Local\Temp\MpCmdRun.log for more information
```

`skipped` mean is in the exclusions paths, for auto finding, we can use [SharpExclusionFinder](https://github.com/Friends-Security/SharpExclusionFinder).

## Reference

+ https://blog.fndsec.net/2024/10/04/uncovering-exclusion-paths-in-microsoft-defender-a-security-research-insight/
+ https://0xdf.gitlab.io/2024/10/26/htb-mist.html#shell-as-svc_web-on-ms01 