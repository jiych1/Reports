### Pixelfed search API is vulnearble to SSRF causing information leak

- **Application**: Pixelfed <= v0.11.9

- **Status**: Fixed, CVE requested

- **Threat Impact**: Sensitive data leakage

- **Code**: Input is retrieved from GET parameter `q`. Then it is saved into `term` property and passed to `Helpers::fetchFromUrl` and then `ActivityPubFetchService::get`, and finally into sink function `Illuminate\Http\Client\PendingRequest::get`

  File: `app/Http/Controllers/SearchController.php`

  ```
  public function searchAPI(Request $request) {
  	...
  	$this->term = $request->input('q');
  	...
  	$this->getPosts();
  }
  
  protected function getPosts() {
  	$tag = $this->term;
  	$remote = Helpers::fetchFromUrl($tag);
  }
  ```

  File: `app/Util/ActivityPub/Helpers.php`

  ```
  public static function fetchFromUrl($url = false) {
  	$res = ActivityPubFetchService::get($url);
  }
  ```

  File: `app/Services/ActivityPubFetchService.php`

  ```
  public static function get($url, $validateUrl = true) {
  	$res = Http::withHeaders($headers)->timeout(30)->connectTimeout(5)->retry(3, 500)->get($url);
  }
  ```

- **POC**:

  1. Listen on 8080 on server side
  1. Send a get request to /api/search, with parameters`url=https%3A%2F%2Fssrf.localdomain1234.pw%2Fimg-without-body%2F301-http-127.0.0.1%3A8080-.i.jpg&q=abc&src=metro&v=2&scope=all`
  1. Receive a request on server side. 
