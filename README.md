# Important Note

Hey there just an update from myself (@KyranRana), I am aware this project is bugged in some instances and doesn't work at all in others. This is why I am developing an entirely new version of it (v4.0.0) which is hopefully going to fix most issues and reduce the rate at which issues are created. This version will be out for early testing soon. This project is quite a lot of work to maintain so if you want to support me please donate: 1Fg3kSZgAo97yBT6dCjkdp1cGteevEGhLZ


# Cloudflare Bypass

[![CI](https://github.com/KyranRana/cloudflare-bypass/workflows/CI/badge.svg)](https://github.com/jaymoulin/cloudflare-bypass/actions?query=workflow%3ACI)

A new and improved PHP library which bypasses the Cloudflare IUAM page using cURL.


#### Installation

With composer:

`composer require kyranrana/cloudflare-bypass`


#### Usage with cURL

Use cURL how you normally would but instead of using `curl_exec` to execute the 
request we provide a class called `CFCurlImpl`. `CFCurlImpl` provides an `exec` method which takes your cURL handle and 
executes it - handling the IUAM page if it appears.

  
Method definition:
  
```
CFCurlImpl->exec(resource $curlHandle, UAMOptions $uamOptions)
``` 

Example:

```php
<?php
use CloudflareBypass\CFCurlImpl;
use CloudflareBypass\Model\UAMOptions;

/*
 * Prerequisites
 *
 * Set the following request headers:
 *
 * - Upgrade-Insecure-Requests
 * - User-Agent
 * - Accept
 * - Accept-Language
 *
 * Set the following options:
 *
 * - CURLINFO_HEADER_OUT    true
 * - CURLOPT_VERBOSE        false
 *
 */
$url = "https://predb.me/?search=720p";
$ch = curl_init($url);

// Want to cache clearance cookies ?
//curl_setopt($ch, CURLOPT_COOKIEJAR, "cookies.txt");
//curl_setopt($ch, CURLOPT_COOKIEFILE, "cookies.txt");

curl_setopt($ch, CURLOPT_FOLLOWLOCATION, true);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLINFO_HEADER_OUT, true);
curl_setopt($ch, CURLOPT_HTTPHEADER,
    array(
        "Upgrade-Insecure-Requests: 1",
        "User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36",
        "Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3",
        "Accept-Language: en-US,en;q=0.9"
    ));

$cfCurl = new CFCurlImpl();

$cfOptions = new UAMOptions();
$cfOptions->setVerbose(true);
// $cfOptions->setDelay(5);

try {
    $page = $cfCurl->exec($ch, $cfOptions);

    // Want to get clearance cookies ?
    //$cookies = curl_getinfo($ch, CURLINFO_COOKIELIST);

} catch (ErrorException $ex) {
    echo "Unknown error -> " . $ex->getMessage();
}
```
