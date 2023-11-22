### Improved File Manager v4.0.0 SSRF

Improved File Manager v4.0.0 is vulnerable to SSRF

Source: `$_REQUEST`

Sink:`file_get_contents`

Code:

src/main.php

```
private function dispatch() {
  ...
  return $this->remoteUpload($_REQUEST);
}

private function remoteUpload(array $d) {
  ...
  file_put_contents($filename, file_get_contents($d['url']));
}
```

POC:

1. Listen on 8080 on server side
2. Using the remote upload function, specify `url=http://localhost:8080`
3. Receive a request on server side. The full response is also disclosed

Status: Acknowledged