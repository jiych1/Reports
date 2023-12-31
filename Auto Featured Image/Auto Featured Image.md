### Auto Featured Image replace thumbnail function is vulnerable to SSRF causing file disclosure and information leak

- **Application**: Auto Featured Image <= 3.9.18 

- **Status**: Confirmed, not patched yet

- **Threat Impact**: Sensitive data leakage, file disclosure

- **Code**: Image URL is retrieved from POST parameter `image`. Then it is used as parameter of function `generate_post_thumb`, which will finally call sink `file_get_contents` with the image URL.

  File: `includes/class-apt.php`

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

- **POC**:
  1. Listen on 8888 on server side
  1. Login as author+
  1. Create a post, then use the change thumb function to generate a request with a valid nonce. Modify the request to remove `thumb_id` and add `image` parameter, value is `http://127.0.0.1:8888`
  1. Receive a request on server side. 
  1. The response is saved in the thumb image
