### CommentLuv <= 3.0.4 SSRF

CommentLuv <= 3.0.4 is vulnerable to SSRF

Source: `$_POST`

Sink: `wp_remote_post`

Code:

commentluv.php

```
function do_click() {
  ...
  $url   = $_POST['url'];
  ...
  wp_remote_post( $url,...);
}
```

POC:

1. Listen on 8080 on server side
2. Go into a post, write a comment, specify website as `http://localhost:8080`
3. Receive a request on server side. 

Status: CVE-2023-49159