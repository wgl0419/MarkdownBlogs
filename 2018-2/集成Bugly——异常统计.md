集成Bugly——异常统计
============
>　　最近app的开发算是告一段落了，需要打包交给别人去测。但是又不想浪费时间等对方写测试文档。于是就打算集成腾讯的bugly，自己在后台看出了什么问题。

　　具体的集成步骤就不说了（Bugly的集成本身就很简单），自行看官方文档。

　　主要说下集成期间踩的坑。一共花了两天。集成bugly到项目中花的时间不超过半小时。但是这两天的时间花到哪里去了呢？

　　同事集成了别人的第三方库。这个库中有一个捕获Crash的工具类，他在集成的时候手一滑给初始化了。这就导致了一个问题，发生Crash的时候。Crash被工具类捕获并抛出。Bugly连根毛都摸不到。也造成了我集成bugly失败的假象。bugly文档最少看了20遍，重新集成了10次。

　　最后，我通过写demo。对照logcat才发现问题所在(CrashTime那一行)。

<br/>
<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/2ed9561120efc14df8b16f0fec47c15acc541715/res/2018-2/bugly_app.png"/></div>


**<div align=center>项目</div>**

<br/>

<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/2ed9561120efc14df8b16f0fec47c15acc541715/res/2018-2/bugly_demo.png"/></div>


**<div align=center>demo</div>**

结束语
-----------
>遇到问题的时候需要多思考，多做对比，多动手。