集成ndk导致的SIGSEGV(SEGV_MAPERR)
============
<br/>
前言
------------
>　　最近集成bugly的异常统计，官方文档推荐sdk和ndk同时集成。  
>　　于是我按照官方文档配置gradle文件。然后运行，程序直接Crash。我在bugly的后台看了一下,觉得应该是.so库的问题。
<br/>
<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-2/bugly_ndk1.png"/></div>

>SIG 是信号名的通用前缀, SEGV 是 segmentation violation 的缩写
在 POSIX 兼容的平台上，SIGSEGV 是当一个进程执行了一个无效的内存引用，或发生段错误时发送给它的信号。SIGSEGV 的符号常量在头文件 signal.h 中定义。因为在不同平台上，信号数字可能变化，因此符号信号名被使用。通常，它是信号11。
对于不正确的内存处理,如当程序企图访问 CPU 无法定址的内存区块时,计算机程序可能抛出 SIGSEGV。操作系统可能使用信号栈向一个处于自然状态的应用程序通告错误，由此，开发者可以使用它来调试程序或处理错误。
在一个程序接收到 SIGSEGV 时的默认动作是异常终止。这个动作也许会结束进程，但是可能生成一个核心文件以帮助调试，或者执行一些其他特定于某些平台的动作。
SIGSEGV可以被捕获。也就是说，应用程序可以请求它们想要的动作，以替代默认发生的动作。这样的动作可以是忽略它、调用一个函数，或恢复默认的动作。在一些情形下，忽略 SIGSEGV 导致未定义行为。

　　在网上搜了很多Android SIGSEGV相关的文章，发现他们的情况跟我的不一样。他们的native相关文件是自己编写的，所以他们可以根据logcat定位错误。而我的不是，我的项目没有自己编写过native相关文件。而且将gradle中的ndk配置注释掉就不会出现Crash。后来，问了一下做过ndk开发的大佬。发现了问题的所在。  

<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-2/bugly_ndk2.png"/></div>

**<div align=center>gradle.jpg</div>**　　　　
<br/>
<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-2/bugly_ndk3.png"/></div>
**<div align=center>jnilibs.jpg</div>**　　　　

　　**原因：**配置ndk后，jinlibs文件夹中还存在其它架构的.so库。手机会优先加载自身架构的.so库。但是高德所需要的.so库该架构没有。导致了内存错误。  
　　**解决方案：**jnilibs文件夹中只保留gradle中配置ndk时对应的架构即可，其他架构全部删除。（ndk配置为armeabi-v7a）