DNMP（Docker + Nginx + MySQL8 + PHP7.4 + Redis + Mongo）是一款全功能的

####原文地址：[基于Docker的一键LNMP安装程序](https://gitee.com/Jhoushuai/dnmp)
## 1.目录结构
```
/
│ 
├── data                        数据库数据目录
│   ├── esdata                  ElasticSearch 数据目录
│   ├── mongo                   MongoDB 数据目录
│   ├── mysql                   MySQL8 数据目录
├── services                    服务构建文件和配置文件目录
│   ├── elasticsearch           ElasticSearch 配置文件目录
│   ├── mysql                   MySQL8 配置文件目录
│   ├── nginx                   Nginx  配置文件目录
│   ├── php                     PHP7.4 配置目录
│   └── redis                   Redis  配置目录
├── logs                        日志目录
├── docker-compose.yml          Docker 服务配置文件
├── env                         环境配置示例文件
└── www                         PHP 代码目录
```


## 2.管理命令
### 2.1 服务器启动和构建命令
```bash
$ docker rm -f $(docker ps -aq )
$ docker ps -a

$ docker images
$ docker rmi -f $(docker images -qa)

```
如需管理服务，请在命令后面加上服务器名称，例如：
```bash
$ docker-compose up                         # 创建并且启动所有容器
$ docker-compose up -d                      # 创建并且后台运行方式启动所有容器
$ docker-compose up nginx php mysql         # 创建并且启动nginx、php、mysql的多个容器
$ docker-compose up -d nginx php  mysql     # 创建并且已后台运行的方式启动nginx、php、mysql容器


$ docker-compose start php                  # 启动服务
$ docker-compose stop php                   # 停止服务
$ docker-compose restart php                # 重启服务
$ docker-compose build php                  # 构建或者重新构建服务

$ docker-compose rm php                     # 删除并且停止php容器
$ docker-compose down                       # 停止并删除容器，网络，图像和挂载卷

```
**重启 Nginx**
```bash
$ docker exec -it nginx nginx -s reload
```
这里两个`nginx`，第一个是容器名，第二个是容器中的`nginx`程序。


### 2.2 添加快捷命令
在开发的时候，我们可能经常使用`docker exec -it`切换到容器中，把常用的做成命令别名是个省事的方法。

首先，在主机中查看可用的容器：
```bash
$ docker ps           # 查看所有运行中的容器
$ docker ps -a        # 所有容器
```
输出的`NAMES`那一列就是容器的名称，如果使用默认配置，那么名称就是`nginx`、`php`、`mysql`等。

然后，打开`~/.bashrc`或者`~/.zshrc`文件，加上：
```bash
alias dnginx='docker exec -it nginx /bin/sh'
alias dphp='docker exec -it php /bin/sh'
lias dmysql='docker exec -it mysql /bin/bash'
alias dredis='docker exec -it redis /bin/sh'
```
下次进入容器就非常快捷了，如进入php容器：
```bash
$ dphp
```

## 3.使用Log
Log文件生成的位置依赖于conf下各log配置的值。
### 3.1 Nginx日志
Nginx日志是我们用得最多的日志，所以我们单独放在根目录`log`下。
`log`会目录映射Nginx容器的`/var/log/nginx`目录，
所以在Nginx配置文件中，需要输出log的位置，我们需要配置到`/var/log/nginx`目录，如：
```
error_log  /var/log/nginx/nginx.localhost.error.log  warn;
```

### 3.2 php日志
如果确实需要，可按一下步骤开启（在容器中）。
1. 进入容器，创建日志文件并修改权限：
    ```bash
    $ docker exec -it php /bin/sh
    $ mkdir /var/log/php
    $ cd /var/log/php
    $ touch php-fpm.error.log
    $ chmod a+w php-fpm.error.log
    ```
2. 主机上打开并修改PHP-FPM的配置文件`conf/php-fpm.conf`，找到如下一行，删除注释，并改值为：
    ```
    php_admin_value[error_log] = /var/log/php/php-fpm.error.log
    ```
3. 重启PHP-FPM容器。

### 3.3 MySQL日志
因为MySQL容器中的MySQL使用的是`mysql`用户启动，
它无法自行在`/var/log`下的增加日志文件。
所以，我们把MySQL的日志放在与data一样的目录，即项目的`mysql`目录下，
对应容器中的`/var/lib/mysql/`目录。
```bash
slow-query-log-file     = /var/lib/mysql/mysql.slow.log
log-error               = /var/lib/mysql/mysql.error.log
```
以上是mysql.conf中的日志文件的配置。



## 4.数据库管理

MySQL连接信息：
- host：(本项目的MySQL容器网络)
- port：`3306`
- username：
- password：



Redis连接信息如下：
- host: (本项目的Redis容器网络)
- port: `6379`


## 5.在正式环境中安全使用
要在正式环境中使用，请：
1. 在php.ini中关闭XDebug调试
2. 增强MySQL数据库访问的安全策略
3. 增强redis访问的安全策略


## 6 常见问题
### 6.2 Docker使用cron定时任务
在主机中的crontab -e中就可以：
```bash
0 2 * */2 * docker exec dnmp_nginx_1 certbot renew     # 每隔两个月的凌晨2点执行更新
```

### 6.3 Docker容器时间
容器时间在.env文件中配置`TZ`变量，
所有支持的时区请看[PHP所支持的时区列表·PHP官网](https://www.php.net/manual/zh/timezones.php)。

### 6.4 如何连接MySQL和Redis服务器
这要分两种情况，

第一种情况，在**PHP代码中**。
```php
// 连接MySQL
$dbh = new PDO('mysql:host=mysql;dbname=mysql', 'root', '123456');

// 连接Redis
$redis = new Redis();
$redis->connect('redis', 6379);
```
因为容器与容器是`expose`端口联通的，
而且在同一个`networks`下，所以连接的`host`参数直接用容器名称，
`port`参数就是容器内部的端口。

第二种情况，**在主机中**通过**命令行**或者**Navicat**等工具连接。
主机要连接mysql和redis的话，要求容器必须经过`ports`把端口映射到主机了。
以 mysql 为例，`docker-compose.yml`文件中有这样的`ports`配置：`3306:3306`，
就是主机的3306和容器的3306端口形成了映射，所以我们可以这样连接：
```bash
$ mysql -h127.0.0.1 -uroot -p123456 -P3306
$ redis-cli -h127.0.0.1
```
这里`host`参数不能用localhost是因为它默认是通过sock文件与mysql通信，
而容器与主机文件系统已经隔离，所以需要通过TCP方式连接，所以需要指定IP。

生成自签名证书
```bash

#第一种：一键生成自签名证书
openssl req -new -x509 -newkey rsa:4096 -keyout test.key -out test.crt

#第二种方式：

生成私钥(key文件)
openssl genrsa -out client.key 4096

生成签名请求(csr文件)
openssl req -new -key client.key -out client.csr

签发证书
openssl x509 -req -days 365 -in client.csr -signkey client.key -out client.crt

```


