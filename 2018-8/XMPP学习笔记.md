#XMPP学习笔记

2018/7/31


##1.XMPP是什么？
>　　 XMPP（Extensible Messaging and Presence Protocol）,可扩展消息与存储协议，是目前主流的IM协议之一，它是一种基于XML传输的协议，我们都知道XML的可扩展性比较强，XMPP使用XML进行传输，使得我们可以使用XMPP发送扩展的消息来满足我们更多的需求。
　　XMPP包含两部分的定义：一是核心的XML流传输协议，此定义使得XMPP能够在一个比以往网络通信协议更规范的平台上实现；二是基于
XML流传输的即时通讯扩展应用。
 　　XMPP 中定义了三个角色:客户端,服务器,网关。通信能够在这三者的任意两个之间双向发生。服务器同时承担了客户端信息记录,连接管理和信息的路由功能。网关承担 着与异构即时通信系统的互联互通,异构系统可以包括 SMS(短信),MSN,ICQ 等。基本的网络形式是单客户端通过 TCP/IP 连接到单服务器,然后在之上传输 XML。

###1.1XMPP寻址(Jid)
>　　xmpp在网络上通信的每个实体都有统一的ID表示，即为JID(jabber identifier);JID有很多不同的形式，通常类似于邮件的形式。
>　　　jid = [user_name]@[domain]/[resource]
>　　　user_name：用户名
>　　　domain：服务器域名（以openfire为例）
>　　　resource：属于用户的位置或设备
>　　　例如：liangfeng@openfire/38lskjzm05



##2.Smack是什么？
>　　Smack是一个开源，高度模块化，易于使用的XMPP客户端库，用Java编写，适用于Java SE兼容的JVM和Android。
>　　它是一个纯Java库，可以嵌入到您的应用程序中，以便从完整的XMPP即时消息客户端创建任何内容，再到简单的XMPP集成，例如发送通知消息和在线启用设备。




##3.openfire是什么？
>　　是免费的、开源的、基于可拓展通讯和表示协议(XMPP)、采用Java编程语言开发的实时协作服务器。 Openfire安装和使用都非常简单，并利用Web进行管理。单台服务器可支持上万并发用户。


##4.Smack用法
>本来是准备用aSmack的，但是那个项目已经好几年没人用了。
>GitHub上搜索Smack，igniterealtime/Smack这个项目。你会在readme中看到下面内容：
>>　　Resources
>　　Current Readme: https://igniterealtime.org/projects/smack/readme

>既然使用这个项目，当然是要看最新的readme。


Android在gradle中配置如下：
```java
    compile 'org.igniterealtime.smack:smack-android-extensions:4.2.0'
    compile 'org.igniterealtime.smack:smack-experimental:4.2.0'
    compile 'org.igniterealtime.smack:smack-tcp:4.2.0'
```

这里我是全部都配置了，你也可以使用官方推荐的配置。


###4.0 Smack中xmpp常用配置
```java
class XmppConfig {
    companion object {
        /**
         * openfire服务器的服务器名称(主机名)
         * domain是配置openfire服务器时填写的域
         */
        val domain = "openfirewebsever"
        /**
         * openfire服务器的主机IP
         * 测试环境下为本机(电脑)的IP
         */
        val hostAddress = "192.168.0.135"
        /**
         * openfire端口号
         * 纯文本连接端口号
         */
        val portText = 5222
        /**
         * openfire端口号
         * 加密连接端口号
         */
        val portEncryption = 5223
    }
}

```

###4.1连接
>smack中的所有操作都有一个前提：客户端（手机）与服务端（openfire）建立连接
>连接操作是有相关监听器的


```java
            //添加连接监听器
            connect?.addConnectionListener(object : ConnectionListener {
                override fun connected(connection: XMPPConnection?) {
                    Log.e(TAG, ">>>>>>>连接成功:" + connection?.isConnected)
                }

                override fun connectionClosed() {
                    Log.e(TAG, ">>>>>>>连接关闭:")
                }

                override fun connectionClosedOnError(e: java.lang.Exception?) {
                    Log.e(TAG, ">>>>>>>出现异常，连接关闭:" + e?.cause)
                    e?.stackTrace?.forEach {
                        Log.e(TAG, "connectionClosedOnError>>>>>>>Exception:" + it?.toString())
                    }
                }

                //重连成功
                override fun reconnectionSuccessful() {
                    Log.e(TAG, "reconnectionSuccessful>>>>>>>重连成功")

                }

                //已认证
                override fun authenticated(connection: XMPPConnection?, resumed: Boolean) {
                    Log.e(TAG, "authenticated>>>>>>>已认证:")
                }

                //重连失败
                override fun reconnectionFailed(e: java.lang.Exception?) {
                    Log.e(TAG, "reconnectionFailed>>>>>>>重连失败:" + e?.cause)
                    e?.stackTrace?.forEach {
                        Log.e(TAG, "reconnectionFailed>>>>>>>Exception:" + it?.toString())
                    }
                }

                //在指定的秒数内重新连接
                override fun reconnectingIn(seconds: Int) {
                    Log.e(TAG, "reconnectingIn>>>>>>>正在重连中_seconds:" + seconds)
                }
            })

```


```java
            var builder = XMPPTCPConnectionConfiguration.builder()
            var serviceName = JidCreate.domainBareFrom(XmppConfig.domain)
            builder?.setXmppDomain(serviceName) //设置openfire服务器名称
                    ?.setHostAddress(InetAddress.getByName(XmppConfig.hostAddress))//设置openfire主机IP
                    ?.setPort(XmppConfig.portText)//设置端口
                    ?.setDebuggerEnabled(true)//
                    ?.setCompressionEnabled(true)//
                    ?.setSendPresence(true)
                    ?.setSecurityMode(ConnectionConfiguration.SecurityMode.disabled)//非安全模式
                    ?.setDebuggerEnabled(true)
					
            var config = builder?.build()
            connect = XMPPTCPConnection(config)
            connect?.connect()

```




###4.2注册
>　　在4.2的版本中，注册操作被封装到了AccountManager中，账号和密码属于必传参数。其它参数可以不传。
>　　比较麻烦的是注册操作是没有回调的，openfire只会返回相关数据包。只能根据注册时的数据包id，在连接中添加过滤器，根据过滤器的回调来判断。
>　　AccountManager中的注册相关api是没有返回值的，但是又需要注册的数据包id来判断注册结果。我这里的处理方法是。将jar包中的AccountManager文件复制到项目中，修改源码添加返回值。使用项目中的AccountManager进行注册操作。


```java
            //数据包过滤器
            var messageFilter = AndFilter(StanzaTypeFilter(Message::class.java))
            var iqFilter = AndFilter(StanzaTypeFilter(IQ::class.java))
            var presenceFilter = AndFilter(StanzaTypeFilter(Presence::class.java))

            //数据包监听器
            var stanzaListener = object : StanzaListener {
                override fun processStanza(packet: Stanza?) {
                    //xml转json
                    var xmlString = packet?.toXML()?.toString()
                    var xmlToJson = XmlToJson.Builder(xmlString!!).build()
                    //json转对象
                    var result = Gson().fromJson(xmlToJson?.toString(), IQBean::class.java)
                    Log.e(TAG, "connect>>>>>>>数据包监听器,监听到数据包xmlString:" + xmlString)
                    Log.e(TAG, "connect>>>>>>>数据包监听器,监听到数据包result:" + result)
                    if (result?.iq?.type?.equals(IQ.Type.result.toString())!! && result?.iq?.id?.equals(registerId)) {//响应成功
                        Log.e(TAG, ">>>>>>>注册成功:")
                    }
                }
            }

            connect?.addAsyncStanzaListener(stanzaListener, messageFilter)
            connect?.addAsyncStanzaListener(stanzaListener, presenceFilter)
            connect?.addAsyncStanzaListener(stanzaListener, iqFilter)

```


```java
fun register(userName: String, passWord: String) {
            try {
                if (connect?.isConnected!!) {
                    Log.e(TAG, ">>>>>>>连接成功_register:" + connect?.isConnected)
                    var accountManager = AccountManager.getInstance(connect)
                    Log.e(TAG, ">>>>>>>服务器是否支持注册:" + accountManager?.supportsAccountCreation())
                    if (accountManager?.supportsAccountCreation()!!) {
                        var attributes = mutableMapOf<String, String>()
                        attributes?.put("name", "小明")
                        attributes?.put("email", "123@qq.com")
                        attributes?.put("first", "first")
                        attributes?.put("last", "last")
                        attributes?.put("city", "city")
                        attributes?.put("state", "state")
                        attributes?.put("zip", "zip")
                        attributes?.put("phone", "phone")
                        attributes?.put("url", "url")
                        attributes?.put("date", "date")
                        attributes?.put("misc", "misc")
                        attributes?.put("text", "text")
                        attributes?.put("remove", "remove")
                        accountManager?.sensitiveOperationOverInsecureConnection(true)//允许在不安全连接创建账号
                        var reg = accountManager?.createAccount(Localpart.from(userName), passWord)
                        registerId = reg?.packetID!!
//                        accountManager?.createAccount(Localpart.from(userName), passWord, attributes)
                    } else {
                        Log.e(TAG, ">>>>>>>服务器不支持注册账号:")
                    }

                } else {//未连接openfire服务器

                }
            } catch (e: XMPPException.XMPPErrorException) {
                Log.e(TAG, ">>>>>>>XMPPErrorException:" + e?.cause)
                e?.stackTrace?.forEach {
                    Log.e(TAG, ">>>>>>>XMPPErrorException:" + it?.toString())
                }
            } catch (e: SmackException.NoResponseException) {
                Log.e(TAG, ">>>>>>>NoResponseException:" + e?.cause)
                e?.stackTrace?.forEach {
                    Log.e(TAG, ">>>>>>>NoResponseException:" + it?.toString())
                }
            } catch (e: SmackException.NotConnectedException) {
                Log.e(TAG, ">>>>>>>NotConnectedException:" + e?.cause)
                e?.stackTrace?.forEach {
                    Log.e(TAG, ">>>>>>>NotConnectedException:" + it?.toString())
                }
            } catch (e: Exception) {
                Log.e(TAG, ">>>>>>>Exception:" + e?.cause)
                e?.stackTrace?.forEach {
                    Log.e(TAG, ">>>>>>>Exception:" + it?.toString())
                }
            } finally {

            }
        }

```

###4.3登录
>登录的操作比较简单，调api传参数即可。
>

```java

        /**
         * 登录
         */
        fun login(userName: String, passWord: String) {
            if (connect?.isConnected!!) {
                connect?.login(userName, passWord)
            }
        }

```

###4.4添加好友
>添加好友时需要传入参数Jid,这里传入注册登录时用的用户名即可。

```java
        fun addFriend(userName: String, nickname: String): Boolean {
            connect?.let {
                try {
                    Roster.getInstanceFor(it)?.createEntry(JidCreate.bareFrom(userName), nickname, null)
                    return true
                } catch (e: Exception) {

                }
            }

            return false
        }

```


###4.5获取好友列表
>api返回的是一个Set集合，我这里处理成了一个list集合。

```java
        fun getFriends(): MutableList<RosterEntry>? {
            connect?.let { con ->
                Roster.getInstanceFor(con)?.entries?.let { entries ->
                    var list = mutableListOf<RosterEntry>()
                    entries?.forEach {
                        list?.add(it)
                    }
                    return list
                }
            }
            return null
        }

```


###4.6删除好友
>删除操作需要传入名册中对应的条目
>条目可以从名册操作(4.5)中获取

```java
        fun deleteFriend(entry: RosterEntry) {
            connect?.let {
                Roster.getInstanceFor(it)?.removeEntry(entry)
            }
        }

```



###4.6发送消息
>这里涉及到chat会话的概念，实际上是通过会话chat发消息。

```java
        fun sendMessage(jid: EntityBareJid): Chat? {
            Log.e(TAG, ">>>>>>>jid是不是isEmpty:" + jid?.isEmpty())
            connect?.let { con ->
                ChatManager.getInstanceFor(con)?.chatWith(jid)?.let { chat ->
                    chat?.send("hello word")
                    return chat
                }
            }
            return null
        }
```