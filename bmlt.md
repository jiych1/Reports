### BMLT Meeting Map <= 2.5 SSRF

bmlt-meeting-map <= 2.5 is vulnerable to SSRF

Source:`$_POST`

Sink: `wp_remote_get`

Code:

meeting_map.php

```
public function admin_options_page() {
  ...
  $this->options['root_server'] = $_POST['root_server'];
}

public function get($url, $cookies = null)
{
  ...
  return wp_remote_get($url, $args);
}

public function testRootServer($root_server)
{
  $results = $this->get("$root_server/client_interface/serverInfo.xml");
}

$this_connected = $this->testRootServer($this->options['root_server']);
```

POC:

1. Listen on 8080 on server side
2. Login, test root server with URL `http://localhost:8080`
3. Receive a request on server side. The full response is also disclosed

Status: Fixed