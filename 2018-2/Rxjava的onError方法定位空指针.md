Rxjava的onError方法定位空指针
======================================
　　最近一直在使用kotlin+Rxjava写项目。碰到一个空指针（那个null是后来定位到了打印出来的）。情况如下：
```kotlit
override fun onError(e: Throwable) {
        Log.e(TAG, "onError:" + e)
    }
```
<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-1/Rxjava_onError_npe0.png"/></div>

　　空指针？小意思。仔细一看：没有定位？
　　没有定位！！！
　　没有定位！！！
　　没有定位！！！
　　就问你绝不绝望？我onNext方法中的操作有40行左右。上哪去找这个空指针？
　　也许你会说：你不会写个全局捕获异常吗？我个人觉得try-catch里面写那么多代码狠low。
　　研究了一个多小时，找到解决方案：

```kotlig
override fun onError(e: Throwable) {
        Log.e(TAG, "onError:" + e)
        e?.stackTrace?.forEach {
            Log.e(TAG, "stackTrace---element:" +it?.toString())
        }
    }
```
<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-1/Rxjava_onError_npe.png"/></div>

结束语
----------------
　　在百度解决方案的时候，真的觉得蛮无奈的。根本没有类似的案例，都是关于错误处理操作符的文章，可能是大神们觉得没必要写出来吧。百度不到，我就只能去看官方的文档了。其实看文档解决问题的速度更快。
　　stackTrace是不能直接打印的，直接打印打出来的是内存值。需要遍历。


参考资料：
-----------------
<a href="http://reactivex.io/RxJava/javadoc/rx/exceptions/Exceptions.html#throwIfFatal(java.lang.Throwable)">Rxjava文档</a>
