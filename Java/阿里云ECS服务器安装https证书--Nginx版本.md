## 阿里云ECS服务器安装https证书--Nginx版本

配置https证书，使得服务器 能通过https进行文件访问，实现iOS(非发布App Store应用)应用通过https链接完成下载和更新。

iOS应用下载链接：itms-services://?action=download-manifest&url=App的plist文件访问地址（必须为https地址），例如：

itms-services://?action=download-manifest&url=https://域名/文件路径/app.plist

#### 1、环境说明

服务器：ESC 

服务器IP地址：ESC 的公网IP

域名：coolfeng.com

基本流程：有可使用的ESC服务器 -> 可使用的域名 -> 申请ssl安全证书(阿里云有免费的ssl证书) -> 下载Nginx使用的证书 -> Nginx配置ssl

#### 2、域名解析到服务器

登录阿里云-控制台-域名-域名列表(在域名右侧有个解析按钮，点击进去【添加解析】进行服务器IP解析)，添加设置：
主机记录这里选择@，记录值就是服务器ip地址

#### 3、申请证书

域名列表，域名值列表对应的右侧有个管理按钮，点击进入域名基本信息，基本信息最下方有个SSL证书：【开启SSL证书】- 跳转证书申请选择(Symantec DV 通配符SSL 证书)，点击申请-跳转到证书版本选择：
a. 选择'免费版DV SSL'，点击立即购买
b. 点击去支付
c.  确认支付
d. 等待证书签发(1个小时内完成签发，一般情况十分钟之内就会签发)
e. 收到证书已签发的信息，刷新证书状态【已签发】在右侧点击下载，下载Nginx 版证书到本地（有 xxxx.pem  和 xxxx.key 两个文件），用于服务器的配置

#### 4、服务器配置Nginx

Nginx假装安装好了的，如果没有安装的，可参考文章后面的Nginx安装。
参考链接：https://blog.csdn.net/qq_32953079/article/details/81975160

a. 进入/nginx/conf/目录，新增cert文件夹($ mkdir cert)
b. 将之前下载下来的 xxx.pem 和  xxx.key 证书导入到cert文件中
c. 编辑 /nginx/conf/ 目录下的 nginx.conf 文件

说明：服务器默认要开启80与443端口，443端口要开启ssl；cert证书如果不放在conf目录下，在配置的时候需要使用证书的完整路径才行，例如放在/nginx/目录下的需要： /usr/local/nginx/cert/xxx.pem & /usr/local/nginx/cert/xxx.key

A. 常规配置443端口服务，配置好（$ nginx -t 检查Nginx配置文件）可使用域名进行访问，没有指向其他网页的，打开是Nginx的欢迎页面 
http://www.coolfeng.cc/ （.cc域名无法备案，已作废，无法访问了）
https://www.coolfeng.cc/

```nginx
# HTTPS server
   server {
       listen       443 ssl;   #开启443端口 
       server_name  www.coolfeng.cc;  #域名
       ssl_certificate      cert/372xxxx.pem; #.pem证书路径
       ssl_certificate_key  cert/372xxxx.key; #.key证书路径 

       ssl_session_cache    shared:SSL:1m;
       ssl_session_timeout  5m;

       ssl_ciphers  HIGH:!aNULL:!MD5;
       ssl_prefer_server_ciphers  on;

       location / {
           root   html;
           index  index.html index.htm;
       }
   }
```

B.配置80端口，输入http://www.coolfeng.cc/也会自动调整到https页面

```nginx
# HTTP server  80端口设置
server {
    listen 80;
    server_name www.coolfeng.cc; #域名
    rewrite ^(.*)$ https://www.coolfeng.cc:443/$1 permanent; #把http的域名请求转成https
}
```

C.Nginx配置http与https共存，端口设置不可重复

```nginx
# http & https
server {
        listen       80 default backlog=2048;
        listen       443 ssl;
        server_name  www.coolfeng.cc; #域名

        ssl_certificate      cert/3728868coolfeng.pem;
        ssl_certificate_key  cert/3728868coolfeng.key;

        location / {
            root   html;
            index  index.html index.htm;
        }
}
```

 **配置ssl时使用 nginx -t 检查Nginx配置文件文件时可能会报错**：

```
nginx: [emerg] the "ssl" parameter requires ngx_http_ssl_module in /usr/local/nginx/conf/nginx.conf 
```

如出现这个错误 可参考博客：https://www.cnblogs.com/ghjbk/p/6744131.html，以下处理方式也是借鉴该博客的处理方式。
切换到源码包：

```
cd  /usr/local/nginx-1.17.0
```

查看nginx的原有模块：

```
 /usr/local/nginx/sbin/nginx -V
```

在configure arguments:后面显示的原有的configure参数如下：

```
--prefix=/usr/local/nginx --with-http_stub_status_module
```

配置新的信息：

```
./configure  --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
```

等上面配置完，运行：

```
make
```

备份原有的nginx:

```
cp /usr/local/nginx/sbin/nginx  /usr/local/nginx/sbin/nginx.bak
```

覆盖将编译好的nginx覆盖原有的（**需要停掉nginx**）:

```
cp ./objs/nginx /usr/local/nginx/sbin/
```

重启nginx，使用用：/usr/local/nginx/sbin/nginx -V 查看是否已经添加成功，或者用nginx -t 检查配置文件就会出现 success

#### 域名备案(非自己的服务器 )

需要服务器拥有者去申请一个<u>**备案服务号**</u>，然后使用该<u>**备案服务号**</u>进行域名备案

#### 阿里云ECS服务器安装Nginx

1、首先安装pcre pcre-devel库，PCRE(Perl Compatible Regular Expressions) 是一个Perl库，perl 兼容的正则表达式库，nginx 的 http 模块使用 pcre 来解析正则表达式，所以需要在 linux 上安装 pcre 库，pcre-devel 是使用 pcre 开发的一个二次开发库。nginx也需要此库。

```
$ yum install -y pcre pcre-devel
```

2、安装zlib库，zlib 库提供了很多种压缩和解压缩的方式， nginx 使用 zlib 对 http 包的内容进行 gzip ，所以需要安装 zlib 库，（如果有安装过就不再需要）

```
$ yum install -y zlib zlib-devel
```

3、安装以上两个库后可以尝试直接安装Nginx，该步骤非必要，如果有错误提示可能还需要安装 GCC和OpenSSL

```
$ yum install gcc-c++
$ yum install -y openssl openssl-devel
```

4、下载Nginx，版本号可根据官网的版本号自行选择，这里选择nginx-1.17.0版本

```
$ wget -c https://nginx.org/download/nginx-1.17.0.tar.gz
```

5、解压安装

```
$ tar -zxvf nginx-1.17.0.tar.gz
$ cd nginx-1.17.0

// 使用默认配置
$ ./configure
```

6、编译安装

```
$ make
$ make install

// 查找安装路径 
$ whereis nginx

// 找到nginx路径进入到sbin目录找到可执行文件nginx，直接启动即可
$ ./nginx 
//  使用IP或域名进行访问，可看到nginx的欢迎页面
```

