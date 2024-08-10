

DNMP是基于docker部署的Nginx、PHP、MySQL开发环境

1. 支持php5.6、php7.0、php7.1、php7.2、php7.3、php7.4、php8.0、php8.1同时运行并可直接项目指定PHP版本

    已支持常见扩展，如bcmath、gd、opcache、pdo_mysql、zip、Redis、xdebug、swoole、MongoDB等，并且可通过对应的Dockerfile中按例添加自己所需要的扩展

2. 支持MySQL5.6、MySQL5.7、MySQL8.0


<br>

## 一、快速使用

1. clone项目：

    ```bash
    git clone https://github.com/soryetong/dnmp.git
    ```

2. 数据配置

    ```bash
    cd dnmp

    # 准备env
    cp .env.example .env

    # 修改env内的内容
    # 不需要指定服务所运行的平台的话可以这样写
    # CONTAINER_PLATFORM=
    ```

3. docker-compose编排

    ```bash
    # 建议命令
    # docker-compose up nginx phpVersion mysqlVersion -d

    docker-compose up nginx php7.4 mysql5.7 -d
    ```

    注意注意📢

    由于docker-compose.yml文件已经包含了上述的所有配置，所以不建议直接执行`docker-compose up -d`，建议先安装必要的php、MySQL版本（如第三步）
    
    如有需要再额外安装所需要的PHP即可，如`docker-compose up php5.6 -d`

4. 服务访问

    在浏览器中访问：http://localhost 就可以看到默认的 `phpinfo()`


> 注意点
>
> 1. nginx的default.conf中默认加载的是php7.4，所以在第一次编排时建议首先部署php7.4，如果不需要php7.4，那么需要先修改 ./config/nginx/conf.d/default.conf内的 fastcgi_pass 指向的PHP版本
>
> 2. 受网络波动的影响，编排时失败可多重试几次


<br>

## MySQL

> 对外暴露的端口：MySQL5.6:33056，MySQL5.7:33057，MySQL8.0:33080

Navicat连接mysql:
主机名/IP地址使用ipconfig中的IPv4地址，端口、用户名、密码使用创建mysql容器时的配置信息

1. 可能出现的问题

1130 - Host '192.168.65.1' is not allowed to connect to this MySQL server

解决办法：

1. 进入相应的容器
```bash
docker exec -it 容器名 bash
```

2. 进入数据库中
```bash
mysql -uroot -p123456
```

3. 切换到mysql并设置root用户的连接权限
```sql
use mysql;
update user set host = '%' where user ='root';
```

4. 刷新权限
```bash
flush privileges;
```


<br>

## Nginx

配置文件位于 ./config/nginx/conf.d，查看default.conf我们可以得知

```bash
# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
location ~ \.php$ {
    fastcgi_pass   php7.4:9000;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include        fastcgi_params;
}
```

主要就是通过这一段来进行版本切换的

所以假设我们需要一个php5.6版本的，那么我们首先通过`docker-compose up php5.6 -d`启动php5.6，然后修改 `fastcgi_pass` 将其指向 `php5.6:9000` 即可

```bash
# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
location ~ \.php$ {
    fastcgi_pass   php5.6:9000;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include        fastcgi_params;
}
```

除此之外，其他配置与Nginx保持一致

当然，如果你改动了docker-compose.yml文件中的端口映射，那么所对应的端口也需要改动


<br>

## 其他

本项目已在macOS(m2)中运行通过

也在阿里云服务器上成功部署了php7.4、php8.0、mysql5.7

![/images/142536.png](/images/142536.png)
