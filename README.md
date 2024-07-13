# Yii

## Create Project
composer create-project --prefer-dist yiisoft/yii2-app-basic basic

php yii serve
php yii serve --port=8888

cd basic
php requirements.php

Apache Web Configuration
```xml
# Set document root to be "basic/web"
DocumentRoot "path/to/basic/web"

<Directory "path/to/basic/web">
    # use mod_rewrite for pretty URL support
    RewriteEngine on
    
    # if $showScriptName is false in UrlManager, do not allow accessing URLs with script name
    RewriteRule ^index.php/ - [L,R=404]
    
    # If a directory or a file exists, use the request directly
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    
    # Otherwise forward the request to index.php
    RewriteRule . index.php

    # ...other settings...
</Directory>
```
## Nginx configruation
```yaml
server {
    charset utf-8;
    client_max_body_size 128M;

    listen 80; ## listen for ipv4
    #listen [::]:80 default_server ipv6only=on; ## listen for ipv6

    server_name mysite.test;
    root        /path/to/basic/web;
    index       index.php;

    access_log  /path/to/basic/log/access.log;
    error_log   /path/to/basic/log/error.log;

    location / {
        # Redirect everything that isn't a real file to index.php
        try_files $uri $uri/ /index.php$is_args$args;
    }

    # uncomment to avoid processing of calls to non-existing static files by Yii
    #location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
    #    try_files $uri =404;
    #}
    #error_page 404 /404.html;

    # deny accessing php files for the /assets directory
    location ~ ^/assets/.*\.php$ {
        deny all;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_pass 127.0.0.1:9000;
        #fastcgi_pass unix:/var/run/php5-fpm.sock;
        try_files $uri =404;
    }

    location ~* /\. {
        deny all;
    }
}
```

## Website url:
https://www.yiiframework.com/doc/guide/2.0/en/start-installation


## Vagrant Commands
```shell
vagrant status
vagrant global-status
vagrant box list
vagrant box add/remove
vagrant up
vagrant reload
```
## Vagrant SSH
```shell
ssh vagrant@localhost -p 2222
> password:-> 

vagrant ssh
> vagrant@127.0.0.1's password:->

vagrant global-status

vbox Ctrl + L

vagrant ssh-config
-->
C:\vagrant>vagrant ssh-config
Host default
  HostName 127.0.0.1
  User vagrant
  Port 2222
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile C:/vagrant/.vagrant/machines/default/virtualbox/private_key
  IdentitiesOnly yes
  LogLevel FATAL
  PubkeyAcceptedKeyTypes +ssh-rsa
  HostKeyAlgorithms +ssh-rsa

cd C:/vagrant/.vagrant/machines/default/virtualbox/
--- make key:
ssh-keygen -y -f private_key > id_rsa.pub
```
## Yii Server Setting:
https://www.yiiframework.com/doc/guide/2.0/zh-cn/start-installation
1. check server variables:
```shell
php requirements.php
or
mv requirements.php to web path and access this php by url:
http://localhost:8080/requirements.php
```
2. web path equel public path.

3. 推荐使用的 Apache 配置
###### 在 Apache 的 httpd.conf 文件或在一个虚拟主机配置文件中使用如下配置。 注意，你应该将 path/to/basic/web 替换为实际的 basic/web 目录。
*设置文档根目录为 "basic/web"
DocumentRoot "path/to/basic/web"
```yml
<Directory "path/to/basic/web">
    # 开启 mod_rewrite 用于美化 URL 功能的支持（译注：对应 pretty URL 选项）
    RewriteEngine on
    # 如果请求的是真实存在的文件或目录，直接访问
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    # 如果请求的不是真实文件或目录，分发请求至 index.php
    RewriteRule . index.php

    # if $showScriptName is false in UrlManager, do not allow accessing URLs with script name
    RewriteRule ^index.php/ - [L,R=404]
    
    # ...其它设置...
</Directory>
```

4. 推荐使用的 Nginx 配置
###### 为了使用 Nginx，你应该已经将 PHP 安装为 FPM SAPI 了。 你可以使用如下 Nginx 配置，将 path/to/basic/web 替换为实际的 basic/web 目录， mysite.local 替换为实际的主机名以提供服务。
```yml
server {
    charset utf-8;
    client_max_body_size 128M;

    listen 80; ## listen for ipv4
    #listen [::]:80 default_server ipv6only=on; ## listen for ipv6

    server_name mysite.test;
    root        /path/to/basic/web;
    index       index.php;

    access_log  /path/to/basic/log/access.log;
    error_log   /path/to/basic/log/error.log;

    location / {
        # Redirect everything that isn't a real file to index.php
        try_files $uri $uri/ /index.php$is_args$args;
    }

    # uncomment to avoid processing of calls to non-existing static files by Yii
    #location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
    #    try_files $uri =404;
    #}
    #error_page 404 /404.html;

    # deny accessing php files for the /assets directory
    location ~ ^/assets/.*\.php$ {
        deny all;
    }
    
    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_pass 127.0.0.1:9000;
        #fastcgi_pass unix:/var/run/php5-fpm.sock;
        try_files $uri =404;
    }

    location ~* /\. {
        deny all;
    }
}
```

使用该配置时，你还应该在 php.ini 文件中设置 cgi.fix_pathinfo=0 ， 能避免掉很多不必要的 stat() 系统调用。

还要注意当运行一个 HTTPS 服务器时，需要添加 fastcgi_param HTTPS on; 一行， 这样 Yii 才能正确地判断连接是否安全。

#### 入口文件index-test.php 是测试环境入口

#### install debug tools:
```shell
php composer.phar require --prefer-dist yiisoft/yii2-debug
or
php composer.phar require --prefer-dist "yiisoft/yii2-debug": "~2.0.0"

composer require --prefer-dist yiisoft/yii2-debug
```
#### yii server run
```shell
yii serve --docroot="frontend/web/"

php -S localhost:8080 -t www (instead of php yii serve)
```
## CRUD
```shell
backend.test/posts/1.. or /

backend.test/posts/page=1&fields=id,title..

backend.test/posts/page=1&expand=comments&fields=id,title,comments.id,comments.title..
```
## Yii sample yii-application
```shell
https://www.yiiframework.com/extension/yiisoft/yii2-app-advanced/doc/guide/2.0/en/start-installation

http://frontend.test/posts?page=1&expand=comments&fields=id,title,body
```
#### CRUD add new modules steps:
```shell
1. ready database table. and migrate
2. ready common/models/new model
                      /query/new model query
3. ready /frontend/resource/new model
4. ready /frontend/controller/new model controller
```








































