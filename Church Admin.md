### Church Admin <= 3.6.57 SSRF

Church admin <= 3.6.57 is vulnerable to SSRF

Source: `$_POST`

Sink: `fopen`

Code:

includes/directory.php

```
$handle = fopen( $_POST['path'], "r");
```

POC:

1. Listen on 8080 on server side
2. When upload csv, modify path parameter to `http://localhost:8080`
3. Receive a request on server side. 

Status: CVE-2023-38515