### Assistant - Every Day Productivity Apps <= 1.3.5 SSRF

Assistant <= 1.3.5 is vulnerable to SSRF

Source: `$_GET`

Sink: `wp_remote_get`

Code:

backend/src/Hooks/ImageProxy.php

```
public function render_image() {
  ...
  $url = urldecode( $_GET['url'] );
  $response = wp_remote_get( $url );
}
```

POC:

1. Listen on 8080 on server side
2. Send a get request to index.php?fl_asst_image_proxy&url=http%3A%2F%2Flocalhost:8080
3. Receive a request on server side.

Status: CVE-2023-5798