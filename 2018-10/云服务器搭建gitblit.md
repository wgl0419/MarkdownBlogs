#云服务器搭建gitblit

##前言
>　　最近公司新立了一个项目。但是，是一个甲方的项目。甲方要求代码放在他们的服务器上，但是我们用的是git进行管理，而甲方用的是SVN。于是我被分配了搭建git的任务。
>　　首选当然是谷歌旗下的gitlab啦，但是我一看他的文档。推荐内存4g，我就不想用了。
>　　又谷歌了一下，发现了gitblit。这是一个纯java写的框架，可以对git的使用者进行权限管理。

##1.gitblit简介
>　　Gitblit 是一个纯 Java 库用来管理、查看和处理 Git 资料库.相当于 Git 的 Java 管理工具，支持linux系统。
　　Git是分布式版本控制系统，它强调速度、数据一致性，并且支持分布式、非线性工作流。它最初由Linus Torvalds在2005年为Linux内核设计和开发，使用GPLv2证书，并从此成为软件开发中使用最广泛的版本控制系统。
　　Gitblit是完全开源的软件，它基于纯粹的Java堆栈，被设计以在Git仓库速度和效率方面胜任从小型到极大型的项目。它很容易学习和上手，并有着闪电般的性能。它在很多方面远胜Subversion、CVS、Perforce和ClearCase等SCM（版本控制）工具，比如，如快速本地分支、易于暂存、多工作流等。

##2.gitblit的安装
```java
[root@mzf opt]# yum -y install java-1.7.0-openjdk*         //要提前安装java环境
[root@mzf opt]# mkdir -p /opt/gitblit
[root@mzf opt]# cd /opt/gitblit
[root@mzf gitblit]# wget http://dl.bintray.com/gitblit/releases/gitblit-1.8.0.tar.gz    //下载gitblit压缩包
[root@mzf gitblit]# tar -zvxf gitblit-1.8.0.tar.gz		//解压，压缩包
[root@mzf gitblit]# ls 
gitblit-1.8.0  gitblit-1.8.0.tar.gz
[root@mzf gitblit]# cd gitblit-1.8.0/
[root@mzf gitblit-1.8.0]# ls 							//查看当前路径下所有的文件（文件夹）
add-indexed-branch.sh  data  ext          gitblit.sh       install-service-centos.sh  install-service-ubuntu.sh  LICENSE             NOTICE              service-centos.sh
authority.sh           docs  gitblit.jar  gitblit-stop.sh  install-service-fedora.sh  java-proxy-config.sh       migrate-tickets.sh  reindex-tickets.sh  service-ubuntu.sh

```
>　　默认配置文件在data/gitblit.properties，可以根据需要自己修改。
>　　这里我使用的是华为云的云服务器，华为云的所有端口好像默认是关闭的。如果需要使用要单独去配置：
>　　　　控制台->弹性云服务器->更多(对应服务器)->更改安全组->

###启动gitlit服务:
```java
[root@mzf gitblit-1.8.0]# java -jar gitblit.jar --baseFolder data
2018-10-31 09:42:19 [INFO ] 
  _____  _  _    _      _  _  _
 |  __ \(_)| |  | |    | |(_)| |
 | |  \/ _ | |_ | |__  | | _ | |_
 | | __ | || __|| '_ \ | || || __|  http://gitblit.com
 | |_\ \| || |_ | |_) || || || |_   @gitblit
  \____/|_| \__||_.__/ |_||_| \__|  1.8.0

2018-10-31 09:42:19 [INFO ] Running on Linux (3.10.0-693.11.1.el7.x86_64)
2018-10-31 09:42:19 [INFO ] Logging initialized @352ms
2018-10-31 09:42:19 [INFO ] Using JCE Unlimited Strength Jurisdiction Policy files
2018-10-31 09:42:19 [INFO ] Generating Gitblit Certificate Authority (/opt/gitblit/gitblit-1.8.0/data/certs/caKeyStore.p12)
2018-10-31 09:42:21 [INFO ] Generating Gitblit Certificate Authority CRL (/opt/gitblit/gitblit-1.8.0/data/certs/caRevocationList.crl)
2018-10-31 09:42:21 [INFO ] Generating SSL certificate for localhost signed by Gitblit Certificate Authority (/opt/gitblit/gitblit-1.8.0/data/serverKeyStore.jks)
2018-10-31 09:42:22 [INFO ] Importing Gitblit Certificate Authority into trust store (/opt/gitblit/gitblit-1.8.0/data/serverTrustStore.jks)
2018-10-31 09:42:22 [INFO ] Setting up HTTPS transport on port 8443 //端口号
2018-10-31 09:42:22 [INFO ]    certificate alias = localhost
2018-10-31 09:42:22 [INFO ]    keyStorePath   = /opt/gitblit/gitblit-1.8.0/data/serverKeyStore.jks
2018-10-31 09:42:22 [INFO ]    trustStorePath = /opt/gitblit/gitblit-1.8.0/data/serverTrustStore.jks
2018-10-31 09:42:22 [INFO ]    crlPath        = /opt/gitblit/gitblit-1.8.0/data/certs/caRevocationList.crl
2018-10-31 09:42:22 [WARN ] Could not open shutdown monitor on port 8081
java.net.BindException: Address already in use (Bind failed)	//通过这里可以看到端口被占用
	at java.net.PlainSocketImpl.socketBind(Native Method)
	at java.net.AbstractPlainSocketImpl.bind(AbstractPlainSocketImpl.java:376)
	at java.net.ServerSocket.bind(ServerSocket.java:376)
	at java.net.ServerSocket.<init>(ServerSocket.java:237)
	at com.gitblit.GitBlitServer$ShutdownMonitorThread.<init>(GitBlitServer.java:503)
	at com.gitblit.GitBlitServer.start(GitBlitServer.java:455)
	at com.gitblit.GitBlitServer.main(GitBlitServer.java:124)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:606)
	at org.moxie.MxLauncher.main(MxLauncher.java:129)
2018-10-31 09:42:22 [INFO ] jetty-9.2.13.v20150730
2018-10-31 09:42:22 [INFO ] NO JSP Support for /, did not find org.eclipse.jetty.jsp.JettyJspServlet
2018-10-31 09:42:23 [INFO ] 
2018-10-31 09:42:23 [INFO ] ----[com.gitblit.manager.IRuntimeManager]----
2018-10-31 09:42:23 [INFO ] Basefolder  : /opt/gitblit/gitblit-1.8.0/data
2018-10-31 09:42:23 [INFO ] Settings    : /opt/gitblit/gitblit-1.8.0/data/gitblit.properties
2018-10-31 09:42:23 [INFO ] JVM timezone: Asia/Shanghai (CST +0800)
2018-10-31 09:42:23 [INFO ] App timezone: Asia/Shanghai (CST +0800)
2018-10-31 09:42:23 [INFO ] JVM locale  : en_US
2018-10-31 09:42:23 [INFO ] App locale  : <client>
2018-10-31 09:42:23 [INFO ] 
2018-10-31 09:42:23 [INFO ] ----[com.gitblit.manager.INotificationManager]----
2018-10-31 09:42:23 [WARN ] Mail service disabled.
2018-10-31 09:42:23 [INFO ] 
2018-10-31 09:42:23 [INFO ] ----[com.gitblit.manager.IUserManager]----
2018-10-31 09:42:23 [INFO ] ConfigUserService(/opt/gitblit/gitblit-1.8.0/data/users.conf)
2018-10-31 09:42:23 [INFO ] 
2018-10-31 09:42:23 [INFO ] ----[com.gitblit.manager.IAuthenticationManager]----
2018-10-31 09:42:23 [INFO ] External authentication disabled.
2018-10-31 09:42:23 [INFO ] 
2018-10-31 09:42:23 [INFO ] ----[com.gitblit.transport.ssh.IPublicKeyManager]----
2018-10-31 09:42:23 [INFO ] FileKeyManager (/opt/gitblit/gitblit-1.8.0/data/ssh)
2018-10-31 09:42:23 [INFO ] 
2018-10-31 09:42:23 [INFO ] ----[com.gitblit.manager.IRepositoryManager]----
2018-10-31 09:42:23 [INFO ] Repositories folder : /opt/gitblit/gitblit-1.8.0/data/git
2018-10-31 09:42:23 [INFO ] Identifying repositories...
2018-10-31 09:42:23 [INFO ] 0 repositories identified with calculated folder sizes in 8 msecs
2018-10-31 09:42:23 [INFO ] Lucene will process indexed branches every 2 minutes.
2018-10-31 09:42:23 [INFO ] Garbage Collector (GC) is disabled.
2018-10-31 09:42:23 [INFO ] Mirror service is disabled.
2018-10-31 09:42:23 [INFO ] Alias 'UTF8', UTF-9 & UTF-18 encodings as UTF-8 in JGit
2018-10-31 09:42:23 [INFO ] Preparing 14 day commit cache. please wait...
2018-10-31 09:42:23 [INFO ] 0 repositories identified with calculated folder sizes in 1 msecs
2018-10-31 09:42:23 [INFO ] built 14 day commit cache of 0 commits across 0 repositories in 1 msecs
2018-10-31 09:42:23 [INFO ] 
2018-10-31 09:42:23 [INFO ] ----[com.gitblit.manager.IProjectManager]----
2018-10-31 09:42:23 [INFO ] 
2018-10-31 09:42:23 [INFO ] ----[com.gitblit.manager.IFederationManager]----
2018-10-31 09:42:23 [INFO ] 
2018-10-31 09:42:23 [INFO ] ----[com.gitblit.tickets.ITicketService]----
2018-10-31 09:42:23 [INFO ] NullTicketService started
2018-10-31 09:42:23 [INFO ] 
2018-10-31 09:42:23 [INFO ] ----[com.gitblit.manager.IGitblit]----
2018-10-31 09:42:23 [INFO ] 
2018-10-31 09:42:23 [INFO ] ----[com.gitblit.manager.IServicesManager]----
2018-10-31 09:42:23 [INFO ] Federation passphrase is blank! This server can not be PULLED from.
2018-10-31 09:42:23 [INFO ] Fanout PubSub service is disabled.
2018-10-31 09:42:23 [ERROR] Failed to start Git Daemon on :9418
...
2018-10-31 09:42:23 [INFO ] Generating RSA-2048 SSH host keypair...
2018-10-31 09:42:25 [INFO ] Generating DSA SSH host keypair...
2018-10-31 09:42:27 [ERROR] Failed to start SSH daemon on :29418
...
2018-10-31 09:42:27 [INFO ] 
2018-10-31 09:42:27 [INFO ] ----[com.gitblit.manager.IFilestoreManager]----
2018-10-31 09:42:27 [INFO ] No filestore metadata file found
2018-10-31 09:42:27 [INFO ] 
2018-10-31 09:42:27 [INFO ] ----[com.gitblit.manager.IPluginManager]----
2018-10-31 09:42:27 [INFO ] PF4J version 1.8.0 in 'deployment' mode
2018-10-31 09:42:27 [INFO ] Enabled plugins: []
2018-10-31 09:42:27 [INFO ] Disabled plugins: []
2018-10-31 09:42:27 [INFO ] No plugins
2018-10-31 09:42:27 [INFO ] 
2018-10-31 09:42:27 [INFO ] All managers started.
2018-10-31 09:42:27 [INFO ] 
2018-10-31 09:42:27 [INFO ] [GitBlitWebApp] init: Wicket core library initializer
2018-10-31 09:42:27 [INFO ] [GitBlitWebApp] init: Wicket extensions initializer
2018-10-31 09:42:28 [INFO ] [GitBlitWebApp] Started Wicket version 1.4.22 in deployment mode
2018-10-31 09:42:28 [INFO ] Started o.e.j.w.WebAppContext@5c5df228{/,file:/opt/gitblit/gitblit-1.8.0/data/temp/webapp/,AVAILABLE}{file:/opt/gitblit/gitblit-1.8.0/gitblit.jar}
2018-10-31 09:42:28 [INFO ] Started ServerConnector@69176437{SSL-HTTP/1.1}{0.0.0.0:8443}
2018-10-31 09:42:28 [INFO ] Started @9052ms //已经开启gitblit

```



