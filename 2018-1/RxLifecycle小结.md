#RxLifecycle小结
　　最近项目中有一个推送语音通知的需求。开会的时候说不能使用第三方。因为我并不知道通知什么时候下发，所以只能不停的去请求后台下发语音通知的接口。  
　　原本我是打算使用死循环去实现，但是觉得有点low。我想起来学习Rxjava的时候，有一个叫做定时器的东西。我决定使用Rxjava实现这个需求。毕竟Rxjava我只会用来与Retrofit结合起来用。一些其他的操作符都没有用过。这次有机会学习并使用新的操作符，当然不能错过。  
　　

	Observable.interval(2, 2, TimeUnit.SECONDS)//两秒发送一次
                .doOnNext(object : Consumer<Long> {
                    override fun accept(t: Long?) {
                        Log.e(TAG, "第 " + t + " 次轮询");
                        currentUserCallRecords()//封装了网络请求的方法
                    }
                }).subscribe(object : Observers.PollingObserver() {
            override fun onComplete() {
                Log.e(TAG, "轮询器>>>onComplete>>>>:")
            }
        })


　　上面的代码就是使用Rxjava的interval方法实现无限轮询的逻辑。  
　　但是通过logcat中打印的log。我发现这个轮询在我退出当前界面的时候，并没停止！！！根据需求，当用户退出当前界面之后就不需要轮询这个接口了。而且这个现象说明我的程序出现了内存泄漏。内存泄漏这种东西我一向是零容忍。  
　　经过一番Google，我找到了一个专门针对这种情况（使用Rxjava导致的内存泄漏）的三方库“RxLifecycle ”。  
　　
#开源项目
　　RxLifecycle地址：<a href="https://github.com/trello/RxLifecycle">https://github.com/trello/RxLifecycle </a>  
　　该项目是为了防止RxJava中subscription导致内存泄漏而诞生的，核心思想是通过监听Activity、Fragment的生命周期，来自动断开subscription以防止内存泄漏。  
　　由于我使用的是kotlin,所以使用的方式会与Java有些不同，具体可以参考下图（从上面链接的页面截取）。  
　　下面是Rxlifecycle的具体用法。  

###1.在build.gradle中集成Rxlifecycle
	// If you want to use Kotlin syntax
	compile 'com.trello.rxlifecycle2:rxlifecycle-kotlin:2.2.1'
###2.使用bindToLifecycle方法与当前View的生命周期进行绑定
	 override fun onStartToDo() {
        Observable.interval(2, 2, TimeUnit.SECONDS)//两秒发送一次
                .bindToLifecycle(this@NoticeHistoryFragment.view!!)//view创建之后执行。看这里！！！！！！！！！！！
                .doOnNext(object : Consumer<Long> {
                    override fun accept(t: Long?) {
                        Log.e(TAG, "第 " + t + " 次轮询");
                        currentUserCallRecords()
                    }
                }).subscribe(object : Observers.PollingObserver() {
            override fun onComplete() {
                Log.e(TAG, "轮询器>>>onComplete>>>>:")
            }
        })
　　两步就完成了Rxlifecycle的使用，是不是很简单（哈哈）。  
　　需要注意的是，要在View创建完之后调用。不然会报空指针（手动滑稽）。bindToLifecycle方法中传入的参数view，所以一定要在view创建完之后调用。  
　　博主是在onStart方法中调用的。  
　　当然这篇文章只是对RxLifecycle最简单的使用。具体的一些高级用法，由于项目比较赶博主也没有具体研究。对高级用法感兴趣的朋友可以参考下面这篇文章：RxAndroid之Rxlifecycle使用。  
　　至于Java中的用法也蛮简单的。可以参考这篇博客：<a href="http://blog.csdn.net/jdsjlzx/article/details/51527542">RxAndroid之Rxlifecycle使用</a>  
　　
#参考资料：

　　　　<a href="">github开源项目RxLifecycle</a>  
　　　　<a href="http://blog.csdn.net/jdsjlzx/article/details/51527542">RxAndroid之Rxlifecycle使用</a>  
　　　　<a href="https://www.jianshu.com/p/11b3ec672812" target="_blank">Android RxJava 实际应用讲解：（无条件）网络请求轮询</a>
　　　　