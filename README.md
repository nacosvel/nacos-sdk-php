# Nacos Client SDK for PHP

[![GitHub Tag](https://img.shields.io/github/v/tag/nacosvel/nacos-sdk-php)](https://github.com/nacosvel/nacos-sdk-php/tags)
[![Total Downloads](https://img.shields.io/packagist/dt/nacosvel/nacos-sdk-php?style=flat-square)](https://packagist.org/packages/nacosvel/nacos-sdk-php)
[![Packagist Version](https://img.shields.io/packagist/v/nacosvel/nacos-sdk-php)](https://packagist.org/packages/nacosvel/nacos-sdk-php)
[![Packagist PHP Version Support](https://img.shields.io/packagist/php-v/nacosvel/nacos-sdk-php)](https://github.com/nacosvel/nacos-sdk-php)
[![Packagist License](https://img.shields.io/github/license/nacosvel/nacos-sdk-php)](https://github.com/nacosvel/nacos-sdk-php)

[Nacos](https://github.com/alibaba/nacos)的PHP客户端，更多关于Nacos的介绍，可以查看[Nacos Wiki](https://github.com/alibaba/nacos/wiki)。

## 安装

推荐使用 PHP 包管理工具 [Composer](https://getcomposer.org/) 安装 SDK：

```bash
composer require nacosvel/nacos-sdk-php
```

## 特性

+ 扩展性强
+ 容易上手
+ 支持 Open-API 鉴权
+ 支持集群

## 开发计划

- [x] 支持认证鉴权
- [x] 支持 [Authorization Token](https://nacos.io/docs/v2/guide/user/auth/) 自动刷新
- [x] 支持认证缓存功能
- [ ] 支持网络防抖
- [ ] 支持集群
- [ ] 支持负载均衡

## 文档

### Open-API 开启鉴权之前

```php
<?php

use Nacosvel\Nacos\NacosClient;
use Nacosvel\Nacos\NacosConfig;

$config   = new NacosConfig('http://127.0.0.1:8848');
$client   = new NacosClient($config);
$response = $client->request('GET', 'nacos/v2/ns/instance/list', [
    'query' => [
        'serviceName' => 'nacosvel/nacos-sdk-php',
    ],
])->response();
```

### Open-API 开启鉴权之后

```php
<?php

use Nacosvel\Nacos\NacosAuth;
use Nacosvel\Nacos\NacosClient;
use Nacosvel\Nacos\NacosConfig;
use Symfony\Component\Cache\Adapter\FilesystemAdapter;

$config   = new NacosConfig('http://127.0.0.1:8848', new NacosAuth('nacos', 'nacos'), new FilesystemAdapter('cache.namespace'));
$client   = new NacosClient($config);
$response = $client->request('GET', 'nacos/v2/ns/instance/list', [
    'query' => [
        'serviceName' => 'nacosvel/nacos-sdk-php',
    ],
])->response();
```

### `NacosResponseInterface` 自定义响应接口

```php
<?php

use Nacosvel\Nacos\NacosAuth;
use Nacosvel\Nacos\NacosClient;
use Nacosvel\Nacos\NacosConfig;
use Nacosvel\Nacos\NacosResponse;
use Symfony\Component\Cache\Adapter\FilesystemAdapter;

$config   = new NacosConfig('http://127.0.0.1:8848', new NacosAuth('nacos', 'nacos'), new FilesystemAdapter('cache.namespace'));
$client   = new NacosClient($config, new NacosResponse());
$response = $client->request('GET', 'nacos/v2/ns/instance/list', [
    'query' => [
        'serviceName' => 'nacosvel/nacos-sdk-php',
    ],
])->response();
```

### `response(ResponseInterface $response)` 自定义响应方法

```php
<?php

use Nacosvel\Nacos\NacosAuth;
use Nacosvel\Nacos\NacosClient;
use Nacosvel\Nacos\NacosConfig;
use Nacosvel\Nacos\NacosResponse;
use Symfony\Component\Cache\Adapter\FilesystemAdapter;

$config   = new NacosConfig('http://127.0.0.1:8848', new NacosAuth('nacos', 'nacos'), new FilesystemAdapter('cache.namespace'));
$client   = new NacosClient($config, new NacosResponse());
$response = $client->request('GET', 'nacos/v2/ns/instance/list', [
    'query' => [
        'serviceName' => 'nacosvel/nacos-sdk-php',
    ],
])->response(function (\Psr\Http\Message\ResponseInterface $response) {
    return $response->getBody()->getContents();
});
```

### 自定义 `HTTP requests Client`

```php
<?php

use GuzzleHttp\Client;
use Nacosvel\Nacos\NacosAuth;
use Nacosvel\Nacos\NacosClient;
use Nacosvel\Nacos\NacosConfig;
use Nacosvel\Nacos\NacosResponse;
use Symfony\Component\Cache\Adapter\FilesystemAdapter;

$config = new NacosConfig('http://127.0.0.1:8848', new NacosAuth('nacos', 'nacos'), new FilesystemAdapter('cache.namespace'));
$client = new NacosClient($config, new NacosResponse(), new Client([]));
$response = $client->request('GET', 'nacos/v2/ns/instance/list', [
    'query' => [
        'serviceName' => 'nacosvel/nacos-sdk-php',
    ],
])->response();
```

### 完整示例

```php
<?php

use GuzzleHttp\Client;
use Nacosvel\Nacos\NacosAuth;
use Nacosvel\Nacos\NacosClient;
use Nacosvel\Nacos\NacosConfig;
use Nacosvel\Nacos\NacosResponse;
use Nacosvel\Nacos\NacosUri;
use Psr\Log\NullLogger;
use Symfony\Component\Cache\Adapter\FilesystemAdapter;

$uri    = new NacosUri('http://127.0.0.1:8848');
$auth   = new NacosAuth('nacos', 'nacos');
$cache  = new FilesystemAdapter('cache.namespace');
$config = new NacosConfig($uri, $auth, $cache);
$client = new NacosClient($config, new NacosResponse(), new Client([]), new NullLogger());
$response = $client->request('GET', 'nacos/v2/ns/instance/list', [
    'query' => [
        'serviceName' => 'nacosvel/nacos-sdk-php',
    ],
])->response();
```

### 状态码

#### Status Code:200

```json
{
    "code": 0,
    "message": "success",
    "data": {
        "name": "DEFAULT_GROUP@@nacosvel/nacos-sdk-php",
        "groupName": "DEFAULT_GROUP",
        "clusters": "",
        "cacheMillis": 10000,
        "hosts": [],
        "lastRefTime": 1724742686597,
        "checksum": "",
        "allIPs": false,
        "reachProtectionThreshold": false,
        "valid": true
    }
}
```

```json
{
    "code": 10000,
    "message": "parameter missing",
    "data": "Required request parameter 'serviceName' for method parameter type String is not present"
}
```

#### Status Code:403

```json
{
    "timestamp": "2024-08-27T15:06:58.210+08:00",
    "status": 403,
    "error": "Forbidden",
    "message": "user not found!",
    "path": "/nacos/v2/ns/instance/list"
}
```

#### Status Code:404

```json
{
    "timestamp": "2024-08-27T15:07:04.003+08:00",
    "status": 404,
    "error": "Not Found",
    "message": "No message available",
    "path": "/nacos/v3/ns/instance/list"
}
```

#### Status Code:500

```json
{
    "timestamp": "2024-08-27T15:06:39.420+08:00",
    "status": 500,
    "error": "Internal Server Error",
    "message": "HTTP Status 500 Internal Server Error"
}
```

## License

Nacosvel Nacos SDK is made available under the MIT License (MIT). Please see [License File](LICENSE) for more information.
