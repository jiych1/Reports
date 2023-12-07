### chyrp-lite tumblr import function is vulnerable to SSRF causing sensitive data leak

- **Application**: chyrp-lite <=2023.02

- **Status**: Fixed, CVE requested

- **Threat Impact**: Sensitive data leakage

- **Code**: The host of tumblr URL is retrieved from POST parameter `tumblr_url` and is passed to function `get_remote`. In `get_remote`, `extract` is used to create variables `$prefix` and `$host` from parsed URL array and finally passed into sink `fsockopen`.

  File: `modules/migrator/migrator.php`

  ```
  public function admin_import_tumblr(){
    $url = rtrim($_POST['tumblr_url'], "/")."/api/read?num=50";
    get_remote($url);
  }
  ```

  File: `includes/helpers.php`

  ```
  function get_remote($url, $redirects = 0, $timeout = 10, $headers = false): string|false {
    extract(parse_url(add_scheme($url)), EXTR_SKIP);
    $connect = @fsockopen($prefix.$host, $port, $errno, $errstr, $timeout);
  }
  ```

- **POC**:
  1. Listen on 8080 on server side
  1. When importing from tumblr, use `url=http://localhost:8080`
  1. Receive a request on server side. 
