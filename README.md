# air framework

一款使用 `C` 语言写成的、现代的高性能轻量级`PHP` `web`框架。

> ####see [README_en.md](README_en.md) for english introduction.

## 特性

### 1. 高性能、轻量级

在所有`PHP``web`框架的对比测试中，**`air framework`** 在并发数(RPS)、CPU及内存占用等多方面评价指标上均以显著优势领先于其他框架。

### 2. 先进的框架理念

高效灵活的路由功能，简单可扩展的`MVC`机制，规范优雅的自动加载。

帮助开发者快速高效搭建从个人到企业、从简单到复杂，各种类型、各种规模的`web`站点。

### 3. 强大的异步并发能力

在实际的`web`应用中，整个请求流程中业务逻辑（主要是同步阻塞的数据库查询和远程API访问）耗时往往远远大于核心模块的加载耗时。

**`air framework`** 从设计之初即立足于解决实际问题，独创 `request/waiter/service` 异步并发请求模式，同时提供强大的数据库异步并发查询和远程HTTP异步并发请求两大特性，帮助开发者在这两大主要耗时场景中轻松获得数倍乃至数十倍的速度提升，从而使整个应用的运行性能得到真正的质的提升。

#### 3.1 异步并发的数据库查询

```php
<?php
/**
 * air\config::path_get('mysql.config.air');
 * @see https://github.com/wukezhan/air/blob/master/tests/mysqli.inc.php
 * @see https://github.com/wukezhan/air/blob/master/tests/004.mysql.phpt
 */
$b1 = new air\mysql\builder('mysql.config.air');
$b2 = new air\mysql\builder('mysql.config.air');
$b3 = new air\mysql\builder('mysql.config.air');
$start = time();
$b1->async()->query('SELECT 1 AS a, sleep(1) AS b');
$b2->async()->query('SELECT 2 AS a, sleep(1) AS b');
$b3->async()->query('SELECT 3 AS a, sleep(1) AS b');
// really start all the async queries
var_dump($b1->data()); // or var_dump($b[0]); // or foreach($b as $k=>$v){...}
var_dump($b2->data());
var_dump($b3->data());
// will totally use 1 second but not 3 seconds
echo time()-$start, "\n";
```

#### 3.2 异步并发的远程HTTP请求

```php
<?php
/**
 * @see https://github.com/wukezhan/air/blob/master/tests/server.inc.php
 * @see https://github.com/wukezhan/air/blob/master/tests/003.curl.phpt
 */
function data2arr($ch, $data){
    return json_decode($data, 1);
}

$c1 = new air\curl();
$c1->async();
$c1->setopt(CURLOPT_RETURNTRANSFER, 1);
$c1->get(local_server::$host_port . '/get.php?hello=world-1');

$c2 = new air\curl();
$c2->async()
    ->setopt(CURLOPT_RETURNTRANSFER, 1)
    ->get(local_server::$host_port . '/json.php?hello=world-2')
    ->on_success('data2arr')
;

echo $c1->data();
var_dump($c2->data());
```

## 文档及实例

* **[快速开始](docs/hello-world/README.md)**
* **实例**
	* [hello-world](docs/hello-world) 入门演示实例
	* [docker-ui](https://github.com/wukezhan/docker-ui) 一个简单的docker管理界面
	* [tests](tests) 测试实例
* **文档**
	* [接口文档](docs/api.md)
	* [air-book](http://air.wukezhan.com) [预备中]
	* [代码补全 & 函数原型](docs/helper/air.php)


## 安装及使用

### 环境要求

* Linux
* PHP 5.4+
    * mysqlnd
    * mysqli
    * curl

```sh
# configure options
./configure --prefix=/path/to/php --enable-mysqlnd --with-mysqli --with-curl
```

### 安装使用

- 一键体验

对于 docker 用户，可以通过以下命令一键体验 **`air framework`**

```sh
# php5
docker run -it -p 2355:80 --name=airstack5 wukezhan/airstack5
# visit
curl http://localhost:2355/
# or
curl http://localhost:2355/hello/world

#php7
docker run -it -p 2357:80 --name=airstack7 wukezhan/airstack7
# visit
curl http://localhost:2357/
# or
curl http://localhost:2357/hello/world
```

- 从源码安装

```sh
# 1. 准备源码
## 下载并解压缩源码包
### php5
https://github.com/wukezhan/air/archive/master.zip
### php7
https://github.com/wukezhan/air/archive/php7.zip
### 或从源码克隆
git clone https://github.com/wukezhan/air

cd air
# 对于克隆源码，可执行以下命令切换到 php7 分支
git checkout php7

# 2. 编译安装
/path/to/php/bin/phpize
./configure --with-php-config=/path/to/php/bin/php-config
make && make install
make test

# 3. 修改配置
vim /path/to/php/lib/php.ini
增加以下配置：
extension = air.so
```

## 更多

对本项目感兴趣的朋友，可以关注微博 [weibo.com/wukezhan](http://weibo.com/wukezhan) 或通过以下方式进行交流：

* QQ群: 141971669
* twitter: [wukezhan](https://twitter.com/wukezhan)
* github: [wukezhan/air](https://github.com/wukezhan/air)
