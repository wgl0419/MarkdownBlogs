**NoClassDefFoundError: okhttp3.logging.HttpLoggingInterceptor**
===

　　今天在调试的时候碰到了这个异常。我在自己的OPPO上运行的时候完全没有问题。但是在另一部小米上运行时就出现这个异常。最开始我以为是okhttp和Retrofit的版本不一致导致的。但是有一部手机可以运行，基本可以排除版本不一致的问题。最初我直接用下面的报错直接进行百度。

>NoClassDefFoundError: okhttp3.logging.HttpLoggingInterceptor
>
　　搜索的结果基本都是和HttpLoggingInterceptor有关的，反而跟这个异常没什么关系了。那么就是关键词出问题了。于是我直接搜索这个异常。但是搜索结果都是结果都是Java相关的。很明显我需要的是Android相关。于是在关键词中加了一个Android。 
 
>Android NoClassDefFoundError
>
　　然后在这片博客中找到了答案<a href="http://blog.csdn.net/u010312949/article/details/72884790">(Android分包) 一个NoClassDefFoundError引发的血案</a>

　　事实上之前碰到过这个bug，但是会有一个明显的数字65535。但是这次没有，所以没有反应过来。我的情况和这片博客中的情况一模一样就不再赘述了。  
　　下面是我之前关于65535的文章：
><a href="http://blog.csdn.net/liangfeng093/article/details/78496714](http://blog.csdn.net/liangfeng093/article/details/78496714">DexIndexOverflowException--65536</a>
><a href="http://blog.csdn.net/liangfeng093/article/details/70225115">引入jar包之Error:Execution failed for task ':app:transformClassesWithDexForDebug</a>


