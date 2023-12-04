### BlossomThemes Email Newsletter <= 2.2.5 SSRF

BlossomThemes Email Newsletter <= 2.2.5 SSRF

Source: `$_POST`

Sink: `wp_remote_get`

Code: 

includes/class-blossomthemes-email-newsletter-functions.php

```
function bten_get_mailing_list() {
  ...
  $ac = new BlossomThemes_Email_Newsletter_Settings;
  $data = $ac->activecampaign_lists( $_POST['bten_ac_api_key'], $_POST['bten_ac_api_url'] );
}
```

includes/class-blossomthemes-email-newsletter-settings.php

```
function activecampaign_lists( $api_key = '', $url = '' ) {
  ...
  $ac = new ActiveCampaign( $url, $api_key );
  ...
  $ac->credentials_test();
}
```

includes/libs/activecampaign/Connector.class.php

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
3. Go to settings, choose active campaign as platform. Use `http://127.0.0.1:8888` as api url and get list.
4. Receive a request on server side. 

Status: Acknowledged