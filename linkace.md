### LinkAce <= v1.12.2 SSRF

LinkAce <= v1.12.2 is vulnerable to SSRF

Source: `Illuminate\Http\Request::input`

Sink: `Illuminate\Http\Client\PendingRequest::get`

Code: 

app/Http/Controllers/FetchController.php

```
  public function htmlForUrl(Request $request): Response
    {
    	...
        $url = $request->input('url');
        $newRequest = Http::timeout(3);

        $response = $newRequest->get($url);
    }
```

POC:

1. Listen on 8080 on server side
2. Send a post request to /fetch/html-for-url, with data `url=http://localhost:8080`
3. Receive a request on server side. The full response is also disclosed

Status: CVE-2023-48006