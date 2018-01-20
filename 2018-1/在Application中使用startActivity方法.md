**在Application中使用startActivity方法**
===
>一不下心在Application中使用了startActivity方法，编译器报如下异常（注意Caused by）：  
```java
java.lang.IllegalStateException: Fatal Exception thrown on Scheduler.
	at io.reactivex.android.schedulers.HandlerScheduler$ScheduledRunnable.run(HandlerScheduler.java:111)
	at android.os.Handler.handleCallback(Handler.java:815)
	at android.os.Handler.dispatchMessage(Handler.java:104)
	at android.os.Looper.loop(Looper.java:224)
	at android.app.ActivityThread.main(ActivityThread.java:5929)
	at java.lang.reflect.Method.invoke(Native Method)
	at java.lang.reflect.Method.invoke(Method.java:372)
	at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:1113)
	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:879)
Caused by: android.util.AndroidRuntimeException: Calling startActivity() from outside of an Activity  context requires the FLAG_ACTIVITY_NEW_TASK flag. Is this really what you want?
```
>根据Caused by的提示，Activity外部调用startActivity（）需要一个flag。
```java
	intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK)
```