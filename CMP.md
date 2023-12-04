### CMP – Coming Soon & Maintenance Plugin <= 4.1.10 SSRF

CMP – Coming Soon & Maintenance Plugin <= 4.1.10 is vulnerable to SSRF

Source: `$_POST`

Sink: `wp_remote_get`

Code: 

niteo-cmp.php

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

POC:

1. Listen on 8888 on server side
2. Login as admin
3. Go to settings, choose a theme that has mail subscription. Go to Content->Subscribe Form, choose mailchimp integration. Use `-ssrf.localdomain.pw/custom%2D30x/?code=301&url=http://127.0.0.1:8888&a=` as api key and retrieve lists.
4. Receive a request on server side. 

Status: Acknowledged