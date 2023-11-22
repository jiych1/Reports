### Pictshare <= commit d349ee8 SSRF

Pictshare <= commit d349ee8 is vulnerable to SSRF

Source: `$_REQUEST`

Sink: `file_get_contents`

Code:

api/geturl.php

```
$url = trim($_REQUEST['url']);
file_put_contents($tmpfile,file_get_contents($url));
```

POC:

1. Listen on 8080 on server side
2. Send a get request to /api/geturl.php, with parameter `url=http://localhost:8080`
3. Receive a request on server side. The full response is also disclosed in file

Status: CVE-2023-48005