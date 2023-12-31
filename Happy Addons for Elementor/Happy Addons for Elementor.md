### Happy Addons for Elementor mailchimp widget is vulnerable to SSRF causing information leak

- **Application**: Happy Addons for Elementor <= 3.9.11

- **Status**: Confirmed, not patched yet

- **Threat Impact**: Sensitive data leakage

- **Code**: The api is retrieved from `global_api` parameter. Then it is passed into `get_mailchimp_lists` function and split by `-`. The second part is then used in sink function `wp_remote_post` as host.

  File: `classes/select2-handler.php`

  ```
  public static function process_mailchimp_list() {
    $global_api = ! empty( $_REQUEST['global_api'] ) ? $_REQUEST['global_api'] : '';
    ...
    $current_api = $global_api;
    ...
    $options = Widget\Mailchimp\Mailchimp_Api::get_mailchimp_lists($current_api);
  }
  ```

  File: `widgets/mailchimp/mailchimp-api.php`

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

- **POC**:

  1. Listen on 8888 on server side
  1. Login as contributor+
  1. Create a new post, use elementor editor. Add a mailchimp widget, use `-ssrf.localdomain.pw/custom%2D30x/?code=302&url=http://127.0.0.1:8888?` for api key and save.
  1. Receive a request on server side. 
