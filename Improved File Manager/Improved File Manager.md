### Improved File Manager remote upload function is vulnerable to SSRF causing file disclosure and information leak

Improved File Manager v4.0.0 is vulnerable to SSRF

- **Application**: Improved File Manager v4.0.0 

- **Status**: Fixed, CVE requested

- **Threat Impact**: Sensitive data leakage, File disclosure

- **Code**: URL is retrieved from request parameter `url` then used by sink function `file_get_contents` directly. 

  File: `src/main.php`

  ```
  private function dispatch() {
    ...
    return $this->remoteUpload($_REQUEST);
  }
  
  private function remoteUpload(array $d) {
    ...
    file_put_contents($filename, file_get_contents($d['url']));
  }
  ```

- **POC**:
  1. Listen on 8080 on server side
  1. Using the remote upload function, specify `url=http://localhost:8080`
  1. Receive a request on server side. The full response is also disclosed
