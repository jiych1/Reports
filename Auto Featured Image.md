### Auto Featured Image <= 3.9.18 SSRF

Auto Featured Image <= 3.9.18 is vulnerable to SSRF

Source: `$_POST`

Sink: `file_get_contents`

Code: 

includes/class-apt.php

```
public function apt_replace_thumbnail() {
  ...
  $img = $_POST['image'];
  ...
  $img = preg_replace( '/(thumbs\/thumbs_)/', '.', $img );
  ...
  $thumb_id = $this->generate_post_thumb( $img, '', $post_id );
}

public function generate_post_thumb( $image, $title, $post_id ) {
  ...
  $imageUrl = $image;
  $file_data = file_get_contents( $imageUrl, false, stream_context_create( $arrContextOptions ) );
  ...
}
```

POC:

1. Listen on 8888 on server side
2. Login as author+
3. Create a post, then use the change thumb function to generate a request with a valid nonce. Modify the request to remove `thumb_id` and add `image` parameter, value is `http://127.0.0.1:8888`
4. Receive a request on server side. 
5. The response is saved in the thumb image

Status: Acknowledged