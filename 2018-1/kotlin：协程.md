kotlin：协程  
===
>
协程是什么？
---------
>1.协程本质上来说就是线程。一种轻量级的线程。
>2.协程虽然也是线程，但是协程挂起时不会阻塞线程：
>>这里的的挂起可以理解为平时Java中的Thread.sleep()操作。
>>在Java中，如果进行Thread.sleep()操作，那么线程将被阻塞（后续的代码逻辑将无法执行）。直到线程苏醒，才会执行后面的代码逻辑。

如何使用协程？
---------
>这里默认使用的是AndroidStudio3.0
>在build.gradle文件中配置kotlinx-coroutines-core依赖：  
	　　compile ''
>
如何启动协程？
------------
>使用launch函数启动协程：
```kotlin
launch（CommonPool）{//协程内部
	//协程中的执行代码
	delay（3000L,TimeUnit.MILLISECONDS）//协程挂起3秒
}
```
>launch函数会返回一个Job类型的对象，Job是协程创建的“后台任务”，它持有该协程的引用。
>
什么事挂起函数？
---------------
	　　使用suspend关键字修饰的函数。
	　　挂起函数只能在协程代码内部调用。简单来说，就是只能在launch函数内部调用。
	　　如果在Thread中使用，编译器将会报错！！！
	　　如果在Thread中使用，编译器将会报错！！！
	　　如果在Thread中使用，编译器将会报错！！！
	　　重要的事情说三遍。
>
什么是runBlocking函数？
--------
	　　这个函数主要是用来桥接普通阻塞代码和和挂起风格的非阻塞代码。这句话比较晦涩。
	　　简单来说呢就是：让你能够在launch函数外部调用挂起函数而不报错。
		下面是具体代码
```kotlin
fun main(args : Array<String>) = runBlocking<Unit>{
	launch（CommonPool）{//协程内部
		//协程中的执行代码
		delay（3000L,TimeUnit.MILLISECONDS）//协程挂起3秒
		}	
		delay（5000L,TimeUnit.MILLISECONDS）//协程挂起5秒，编译器不会报错
	}
```

如何让协程参加到主线程中？
-----------
>１.协程在挂起时是不会执行逻辑的
>２.当协程挂起时，如果主线程中的逻辑执行完毕。协程回复后是不会执行的。也就是说挂起的协程并没有参与到主线程的时间顺序中。
>　我们可以通过调用join函数解决2中的问题：
```kotlin
fun main(args : Array<String>) = runBlocking<Unit>{
	var c = launch（CommonPool）{//协程内部
		//协程中的执行代码
		delay（5000L,TimeUnit.MILLISECONDS）//协程挂起5秒
		println("666")
	}
	c.join()//调用join后666就会打印出来
	delay（3000L,TimeUnit.MILLISECONDS）//协程挂起3秒，编译器不会报错
}
```

如何取消协程？
-------------

>launch函数会返回一个Job对象，我们可以通过Job的cancel函数取消协程：
```kotlin
fun main(args : Array<String>) = runBlocking<Unit>{
	var job = launch（CommonPool）{//协程内部
		//协程中的执行代码
		delay（5000L,TimeUnit.MILLISECONDS）//协程挂起5秒
		println("666")
	}
	job.cancel()//调用cancel后666就不会打印出来
	delay（3000L,TimeUnit.MILLISECONDS）//协程挂起3秒，编译器不会报错
}
```