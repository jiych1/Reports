### Foodcoopshop <= 3.6.0 SSRF

Foodcoopshop <= 3.6.0 is vulnerable to SSRF

Source: `Cake\Http\ServerRequest::getData`

Sink: `copy`

Code: 

plugins/Network/src/Controller/ApiController.php

```
public function updateProducts()
{
  $productsData = $this->getRequest()->getData('data.data');
  ...
  $products2saveForImage = [];
  foreach ($productsData as $product) {
     ...
     $products2saveForImage[] = [
       $productIds['productId'] => $product['image']
     ];
  }
  ...
  $this->Product->changeImage($products2saveForImage);
}
```

src/Model/Table/ProductsTable.php

```
public function changeImage($products) {
  foreach ($products as $product) {
	$productId = key($product);
	$imageFromRemoteServer = $product[$productId];
	...
	$remoteFileName = preg_replace('/-home_default/', $options['suffix'], $imageFromRemoteServer);
	copy($remoteFileName, $thumbsFileName);
}
```

POC:

1. Listen on 8080 on server side
2. Send a post request to /api/updateProducts.json, with data `data[data][0][remoteProductId]=352&data[data][0][image]=http://localhost:8000/`
3. Receive a request on server side. 
4. The image validity check can be bypassed by using a custom server that returns 200 on HEAD requests, then return a valid image on first GET request and then a 302 redirect to final target on second GET request

Status: CVE-2023-46725