### icms2 <= 2.16.0 SSRF

icms2 <= 2.16.0 is vulnerable to SSRF

Source: `$_POST`

Sink: `curl_exec`

Code:

system/core/uploader.php

```
 public function uploadFromLink($post_filename, $allowed_ext = false, $allowed_size = 0, $destination = false) {
   $link = trim($_POST[$post_filename]);
   ...
   $file_bin = file_get_contents_from_url($link);
 }
```

system/libs/files.helper.php

```
function file_get_contents_from_url($url, $timeout = 5, $json_decode = false, $params = []) {
  ...
  $curl = curl_init();
  ...
  curl_setopt($curl, CURLOPT_URL, $url);
  ...
  curl_exec($curl);
}
```

POC:

1. Listen on 8080 on server side
2. Upload image from url with `url=http://localhost:8080`
3. Receive a request on server side. 

Status: CVE-2023-4878