### chyrp-lite <=2023.02 SSRF

chyrp-lite <=2023.02 is vulnerable to SSRF

Source: `$_POST`

Sink: `fsockopen`

Code:

modules/migrator/migrator.php

```
public function admin_import_tumblr(){
  $url = rtrim($_POST['tumblr_url'], "/")."/api/read?num=50";
  get_remote($url);
}
```

includes/helpers.php

```
function get_remote($url, $redirects = 0, $timeout = 10, $headers = false): string|false {
  extract(parse_url(add_scheme($url)), EXTR_SKIP);
  $connect = @fsockopen($prefix.$host, $port, $errno, $errstr, $timeout);
}
```

POC:

1. Listen on 8080 on server side
2. When importing from tumblr, use `url=http://localhost:8080`
3. Receive a request on server side. 

Status: Fixed, requested