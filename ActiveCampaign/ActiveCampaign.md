### ActiveCampaign API URL credential test vulnerable to SSRF causing sensitive data leakage

- **Application**: ActiveCampaign <= 8.1.13

- **Status**: Confirmed, not patched yet

- **Threat Impact**: Sensitive data leakage

- **Code**: Tainted parameter `api_url` is used to construct class `ActiveCampaignWordPress` which saves the tainted parameter in property `url`. When `credentials_test` is called, the tainted `url` is used as host in `test_url` and a request will be sent through `wp_remote_get`, causing SSRF.

  File: `activecampaign.php`
  
  ```
  function activecampaign_plugin_options() {
    ...
    $ac = new ActiveCampaignWordPress($_POST["api_url"], $_POST["api_key"]);
    $ac->credentials_test();
  }
  ```
  
  File: `activecampaign-api-php/Connector.class.php`
  
  ```
  function __construct($url, $api_key, $api_user = "", $api_pass = "")
  {
    $this->url_base = $this->url = $url;
  }
  ```
  
  File: `activecampaign-api-php/Connector.class.php`
  
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

- **POC**:
  1. Listen on 8888 on server side
  1. Login as admin
  1. Go to settings page, use `http://127.0.0.1:8888` as api url and save
  1. Receive a request on server side. 


