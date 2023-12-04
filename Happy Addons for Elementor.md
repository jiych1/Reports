### Happy Addons for Elementor <= 3.9.11 SSRF

Happy Addons for Elementor <= 3.9.11 is vulnerable to SSRF

Source: `$_REQUEST`

Sink: `wp_remote_post`

Code: 

classes/select2-handler.php

```
public static function process_mailchimp_list() {
  $global_api = ! empty( $_REQUEST['global_api'] ) ? $_REQUEST['global_api'] : '';
  ...
  $current_api = $global_api;
  ...
  $options = Widget\Mailchimp\Mailchimp_Api::get_mailchimp_lists($current_api);
}
```

widgets/mailchimp/mailchimp-api.php

```
public static function get_mailchimp_lists( $api = null ) {
  ...
  if ( $api != null ) {
			self::$api_key = $api;
  }
  $server = explode( '-', self::$api_key );
  ...
  $url = 'https://' . $server[1] . '.api.mailchimp.com/3.0/lists';

		$response = wp_remote_post(
			$url,
			[
				'method'      => 'GET',
				'data_format' => 'body',
				'timeout'     => 45,
				'headers'     => [

					'Authorization' => 'apikey ' . self::$api_key,
					'Content-Type'  => 'application/json; charset=utf-8',
				],
				'body'        => '',
			]
		);
}
```

POC:

1. Listen on 8888 on server side
2. Login as contributor+
3. Create a new post, use elementor editor. Add a mailchimp widget, use `-ssrf.localdomain.pw/custom%2D30x/?code=302&url=http://127.0.0.1:8888?` for api key and save.
4. Receive a request on server side. 

Status: Acknowledged