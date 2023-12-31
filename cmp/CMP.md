### CMP – Coming Soon & Maintenance Plugin mailchimp integration is vulnerable to SSRF causing information leak

- **Application**: CMP – Coming Soon & Maintenance Plugin <= 4.1.10

- **Status**: Confirmed, not patched yet

- **Threat Impact**: Sensitive data leakage

- **Code**: 

  File: `niteo-cmp.php`

  ```
  public function cmp_mailchimp_list_ajax( $apikey ) {
    ...
    $params = $_POST['params'];
    $api_key = $params['apikey'];
    $dc = substr($api_key,strpos($api_key,'-')+1);
    ...
    $response = wp_remote_get( 'https://'.$dc.'.api.mailchimp.com/3.0/lists/', $args );
  }
  ```

- **POC**:
  1. Listen on 8888 on server side
  1. Login as admin
  1. Go to settings, choose a theme that has mail subscription. Go to Content->Subscribe Form, choose mailchimp integration. Use `-ssrf.localdomain.pw/custom%2D30x/?code=301&url=http://127.0.0.1:8888&a=` as api key and retrieve lists.
  1. Receive a request on server side. 
