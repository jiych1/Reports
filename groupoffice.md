### Group Office <= 6.8.14 SSRF

Group Office <= 6.8.14 is vulnerable SSRF

Source: `$_GET`

Sink: `go\core\http\Client::download`

Code:

www/api/upload.php

```
$httpClient = new Client();
$response = $httpClient->download($_GET['url'], $tmpFile);
```

POC:

1. Listen on 8080 on server side
2. Send a get request to /api/upload.php with parameter `url=`http://localhost:8080`
3. Receive a request on server side. Record blobid. The full response is disclosed via /api/download.php?blob=blobid

Status: CVE-2023-46730