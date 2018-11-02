#gitlab使用教程


##0.准备工作
>　　操作系统：Linux(CentOS7)
>　　Linux辅助工具：Xshell6、Xftp5
>　　gitlab官方安装文档：https://about.gitlab.com/install/#centos-7


##1.安装gitlab

###1.1配置环境(Linux指令)
```java
#在root根目录下执行下列指令
sudo yum install -y curl policycoreutils-python openssh-server
sudo systemctl enable sshd
sudo systemctl start sshd
sudo firewall-cmd --permanent --add-service=http
sudo systemctl reload firewalld

```

###1.2配置Postfix发送邮件(可以跳过)
```java
#在root根目录下执行下列指令
sudo yum install postfix
sudo systemctl enable postfix
sudo systemctl start postfix

```

###1.3获取gitlab安装包
>　　下面的指令默认获取最新版本的gitlab(企业版，Enterprise Edition)
```java
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash
```

>　　下面的指令默认获取最新版本的gitlab(社区版，Community Edition)
```java
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
```
>具体区别参考这个链接中的文档：https://about.gitlab.com/install/ce-or-ee/?distro=centos-7 (本教程使用的社区版)

###1.4安装gitlab
```java
#如果没有购买域名的话，将域名缓存服务器ip即可。例如，将下面的URL改成"http://114.116.31.237"
#已购买域名，则直接填入指定域名
sudo EXTERNAL_URL="http://gitlab.example.com" yum install -y gitlab-ce
#安装完成后启动gitlab
gitlab-ctl start
```

###1.5修改配置信息
>　　配置域名的时间一般较长(需要备案)，所以当拿到域名之后需要修改安装时的初始配置。
>　　对应的文件路径：/etc/gitlab/gitlab.rb (gitlab.rb即为配置文件)
>　　使用Xftp连接到服务器，将gitlab.rb文件拷贝到本地使用编辑器修改EXTERNAL_URL字段的内容即可
>　　修改之后重新加载配置：
```java
gitlab-ctl reconfigure
gitlab-ctl restart
```


###1.6配置服务器的端口
>　　gitlab的默认端口时80，如果怕冲突可以换成8888或者其他的没有使用的端口号。
>　　如果直接访问:"http://114.116.31.237:80" 出现502,说明端口被占用或者端口没有开启。这个时候就需要检查一下端口。


###1.7添加本地域名解析
>　　在没有购买域名的情况下，又不想通过ip来访问gitlab。那么就需要在本地添加本地域名解析。
>　　使用编辑器打开C:\Windows\System32\drivers\etc下的hosts文件，在最下面添加如下配置
```java
192.168.222.89 gitlab.xiehe.com
```
>　　配置完后，就可以在浏览器中通过输入http://gitlab.xiehe.com 来访问gitlab了


##2.使用gitlab

###2.1登录gitlab
>　　访问成功之后，gitlab会要求你为root账户设置密码。设置密码之后就可以登录了



###2.2gitlab权限
>　　官方文档：https://gitlab.com/help/user/permissions.md

























