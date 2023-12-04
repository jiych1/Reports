### ActiveCampaign <= 8.1.13 SSRF

ActiveCampaign <= 8.1.13 SSRF is vulnerable to SSRF

Source: `$_POST`

Sink: `wp_remote_get`

Code: 

activecampaign.php

```
function activecampaign_plugin_options() {
  ...
  $ac = new ActiveCampaignWordPress($_POST["api_url"], $_POST["api_key"]);
  $ac->credentials_test();
}
```

activecampaign-api-php/Connector.class.php

```
function __construct($url, $api_key, $api_user = "", $api_pass = "")
{
  $this->url_base = $this->url = $url;
}
```

activecampaign-api-php/Connector.class.php

```
public function credentials_test()
{
  $test_url = "{$this->url}&api_action=user_me&api_output={$this->output}";
  $r = $this->curl($test_url);
}

public function curl($url, $params_data = array(), $verb = "", $custom_method = "") {
  $response = wp_remote_get($url, $args);
}
```

POC:

1. Listen on 8888 on server side
2. Login as admin
3. Go to settings page, use `http://127.0.0.1:8888` as api url and save
4. Receive a request on server side. 

Status: Acknowledged