面试复习之Activity
================
1.生命周期
----------
</n>
<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-6/activity_lifecycle.png"/></div>


>关于生命周期需要注意的：
>>1.onCreate()和onDestory()
>>>在activity从启动到销毁的整个过程中，这两个方法都只会被调用一次。不会出现重复调用的情况。

> 
>>2.onStart()和onStop()
>>>这两个方法分别表示activity可见和不可见。这里“可见”指的是对Android系统可见（出现在后台），而不是用户肉眼可以看见（只有出现在前台，用户才能看见）。可能会出现多次调用的情况。

>>3.onResume和onPause()
>>>这两个方法直接翻译是恢复和暂停的意思。分别代表activity出现和消失在前台。当activity出现在前台时，用户是可以看见并进行一些操作的（例如点击）。可能会出现多次调用的情况。

2.IntentFilter（意图过滤器）
---------------
>IntentFilter主要用于activity的隐式调用
>隐式调用需要Intent能够匹配目标组件的IntentFilter(同时匹配action、category、data)，才能启动目标activity。
>IntentFilter中的过滤信息有action、category、data三种：
>>action:
>>>action是字符串，两个字符串的值相同即为action匹配。当出现多个action时，只需与其中一个匹配，即为action匹配。
>
>>category:
>>>一般不设置这个参数，系统会默认添加category：“android.intent.category.DEFAULT”。
>
>>data:
>>>data有mimeType和URI组成:
>>>mimeType指媒体类型，例如imge/jpeg,audio/mpeg4-generic等。可以表示图片、文本、视频等不同的媒体格式。
>>>URI包括下面属性：
>>>android:scheme 匹配url中的前缀，除了“http”、“https”、“tel”...之外，我们可以定义自己的前缀
>>>android:host 匹配url中的主机名部分，如“google.com”，如果定义为“*”则表示任意主机名
>>>android:port 匹配url中的端口
>>>android:path 匹配url中的路径
>>>```
<scheme>://<host>:<port>/<path>
http://www.baidu.com:80/search/info
```

3.Activity启动过程
---------------
<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-6/activity_boot_process.gif"/></div>

>Step 1. 无论是通过Launcher来启动Activity，还是通过Activity内部调用startActivity接口来启动新的Activity，都通过Binder进程间通信进入到ActivityManagerService进程中，并且调用ActivityManagerService.startActivity接口

>Step 2. ActivityManagerService调用ActivityStack.startActivityMayWait来做准备要启动的Activity的相关信息；

>Step 3. ActivityStack通知ApplicationThread要进行Activity启动调度了，这里的ApplicationThread代表的是调用ActivityManagerService.startActivity接口的进程，对于通过点击应用程序图标的情景来说，这个进程就是Launcher了，而对于通过在Activity内部调用startActivity的情景来说，这个进程就是这个Activity所在的进程了

>Step 4. ApplicationThread不执行真正的启动操作，它通过调用ActivityManagerService.activityPaused接口进入到ActivityManagerService进程中，看看是否需要创建新的进程来启动Activity

>Step 5. 对于通过点击应用程序图标来启动Activity的情景来说，ActivityManagerService在这一步中，会调用startProcessLocked来创建一个新的进程，而对于通过在Activity内部调用startActivity来启动新的Activity来说，这一步是不需要执行的，因为新的Activity就在原来的Activity所在的进程中进行启动

>Step 6. ActivityManagerServic调用ApplicationThread.scheduleLaunchActivity接口，通知相应的进程执行启动Activity的操作

> Step 7. ApplicationThread把这个启动Activity的操作转发给ActivityThread，ActivityThread通过ClassLoader导入相应的Activity类，然后把它启动起来


参考资料：
-------------
>https://blog.csdn.net/luoshengyang/article/details/6689748
>https://blog.csdn.net/luoshengyang/article/details/6685853