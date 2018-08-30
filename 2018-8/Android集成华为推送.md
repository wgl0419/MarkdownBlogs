#Android集成华为推送
##1.<a href="http://developer.huawei.com/consumer/cn">注册成为开发者</a>
>　　这个没什么好讲的，照做就行了。之后就是在控制台创建应用巴拉巴拉的
>　　需要注意的是SHA256指纹：
>>　　SHA256你在控制台可以填好几个，博主建议填两个。一个是debugkeystore的SHA256（也就是你本机的SHA256），另一个是.jks签名文件里面的SHA256。
>

##2.配置HMS Agent
>　　关于HMS Agent的配置其实文档说的也很清楚，但是要仔细看。
>　　下载HMS SDK Agent压缩包，解压。
>　　解压出来的文件里面有个readme，一定要看！！！！
>　　解压出来的文件里面有个readme，一定要看！！！！
>　　解压出来的文件里面有个readme，一定要看！！！！
>　　用GetHMSAgent_*.bat文件（双击）从agent 代码模块（hmsagents 文件夹）中抽取需要模块的agent代码。抽取后的代码放在了同目录下的copysrc目录下
>　　运行脚本的时候你需要填包名，appid和cpid。cpid没有可以直接填appid。然后，脚本会问你：是否需要基础支付、社交巴拉巴拉的。记住：我们只需要Push！！！！
>　　脚本运行完之后，同级目录下会生成一个copysrc文件夹。
>　　复制com文件夹下的huawei文件夹到你项目的com包下，记住是com包下！！！！（否则编译一下导包会出问题）

##3.使用gradle集成SDK

```java
allprojects {
            repositories {
                jcenter()
                maven {url 'http://developer.huawei.com/repo/'}
            }
        }    

```

```java
dependencies {
    compile 'com.huawei.android.hms:push:{version}'         
  }        
```

##4.配置manifest
>　　根据文档来，配置到步骤7即可。
>　　HuaweiPushRevicer可以从官方的demo直接复制过来（注意文件最上面的包名路径）
>　　


##5.初始化，连接服务器，获取token
>初始化
>
```java
        HMSAgent.init(this)
```

>连接服务器
>
```java
       HMSAgent.connect(this, object : ConnectHandler {
            override fun onConnect(rst: Int) {
                LogUtils.e(TAG, "rst:" + rst)
            }
        })
```
>获取token
>
```java
        HMSAgent.Push.getToken(object : GetTokenHandler {
                    override fun onResult(rst: Int) {
                        ToastUtils.showLong("连接token：" + it)
                        LogUtils.e(TAG, "token:" + it)
                    }
                })
```

>　　token会回传到你在第4中创建的HuaweiPushRevicer里面的onToken方法，用这个token去控制台发送推送即可