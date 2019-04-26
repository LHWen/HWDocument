### VULTR 搭建vps服务流程（Mac版本）

1、注册登录，[https://www.vultr.com/?ref=7862204](https://www.vultr.com/?ref=7862204)（本人推荐链接，不强求！可使用[https://www.vultr.com](https://www.vultr.com)）

2、参考教程[https://yq.aliyun.com/articles/620271](https://yq.aliyun.com/articles/620271)，ping服务器地址连接[http://ping.chinaz.com](http://ping.chinaz.com)，如果添加的服务器IP在这个网站上显示当前地区能访问得通该IP可以使用（看延迟高低），否者删除释放改服务器重新选择其他地区服务器（不会另收费）

3、注册成功后先充值，可支付宝或微信充值，最低10美元
![1](https://github.com/LHWen/HWDocument/blob/master/VPSImg/1.png)
选择服务器地区
![2](https://github.com/LHWen/HWDocument/blob/master/VPSImg/2.png)
选择类型与规格
![3](https://github.com/LHWen/HWDocument/blob/master/VPSImg/3.png)
开始创建服务器
![4](https://github.com/LHWen/HWDocument/blob/master/VPSImg/4.png)
创建成功
![5](https://github.com/LHWen/HWDocument/blob/master/VPSImg/5.png)
这是需要检测当前地区网络是否能ping通该IP（[http://ping.chinaz.com](http://ping.chinaz.com)），在IP检查时发现，自己所在地区的网络是超时的，该IP是无法使用的，所以需要关闭该远程服务器并释放掉，然后另外选择其他地区的服务器

服务器详情
![6](https://github.com/LHWen/HWDocument/blob/master/VPSImg/6.png)

4、启动终端，登录远程服务器：ssh root@你的服务器IP地址，ssh root@ip 后无反应情况，请关闭该远程服务器并释放掉，选择其他地区服务器
![7](https://github.com/LHWen/HWDocument/blob/master/VPSImg/7.png)

5、安装ss服务端：apt-get install shadowsocks
![8](https://github.com/LHWen/HWDocument/blob/master/VPSImg/8.png)

6、等ss服务端安装完成，编辑ss的配置文件：vim /etc/shadowsocks/config.json

```
a. i进入编辑状态，开始修改IP跟密码
b. esc退出编辑
c. :wq 保存配置文件
```

初始配置文件
![9](https://github.com/LHWen/HWDocument/blob/master/VPSImg/9.png)
推荐配置
![10](https://github.com/LHWen/HWDocument/blob/master/VPSImg/10.png)

7、编辑完成，开启ss服务进程：ssserver -c /etc/shadowsocks/config.json start &
![11](https://github.com/LHWen/HWDocument/blob/master/VPSImg/11.png)

8、使用客户端工具，进行客户端登陆，然后进行网页测试，看看是否成功

