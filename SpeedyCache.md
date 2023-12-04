### SpeedyCache <= 1.1.2 SSRF

SpeedyCache <= 1.1.2

Source: `$_GET`

Sink: `wp_remote_get`

Code: 

main/ajax.php

```
function speedycache_create_test_cache(){
	
	$url = esc_url($_GET['url']);

	$res = wp_remote_get($url . '?test_speedycache=1', array('timeout' => 30, 'headers' => ['User-agent' => 'SpeedyCacheTest']));

}
```

POC:

1. Listen on 8888 on server side
2. Login as admin
3. Intercept any ajax request sent by the plugin to get a valid nonce, e.g. use the test page speed function.
4. Change the request to GET, with query parameters `http%3A%2F%2F127.0.0.1%3A8888&security=baf058885d&action=speedycache_create_test_cache`
5. Receive a request on server side. 

Status: CVE-2023-49746