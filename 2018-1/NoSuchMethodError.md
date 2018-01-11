#NoSuchMethodError
　在使用高德地图的时候出现了这个bug，下意识的去百度。结果查到的都是关于系统版本过低找不到方法。但是我之前集成高德地图是没有问题的，可以运行。而且报错的代码并不是调用的系统方法，而是高德sdk中的方法。于是查了一下gradle文件，发现项目依赖了一个外部的arr文件。问了一下arr文件的提供方，是否集成了高德地图的sdk。对方回答集成了定位和地图。  
  
　显而易见，是包冲突导致的这个错误。

	java.lang.NoSuchMethodError: No virtual method setHttpTimeOut(J)Lcom/amap/api/location/AMapLocationClientOption; in class Lcom/amap/api/location/AMapLocationClientOption; or its super classes (declaration of 'com.amap.api.location.AMapLocationClientOption' appears in /data/app/com.shtoone.smarthelmet-1/split_lib_dependencies_apk.apk:classes6.dex)
                                                                           at com.shtoone.smarthelmet.service.LocationService.startLocation(LocationService.kt:140)
                                                                           at com.shtoone.smarthelmet.service.LocationService.onCreate(LocationService.kt:32)
                                                                           at android.app.ActivityThread.handleCreateService(ActivityThread.java:3147)
                                                                           at android.app.ActivityThread.access$2000(ActivityThread.java:192)
                                                                           at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1683)
                                                                           at android.os.Handler.dispatchMessage(Handler.java:111)
                                                                           at android.os.Looper.loop(Looper.java:224)
                                                                           at android.app.ActivityThread.main(ActivityThread.java:5929)
                                                                           at java.lang.reflect.Method.invoke(Native Method)
                                                                           at java.lang.reflect.Method.invoke(Method.java:372)
                                                                           at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:1113)
                                                                           at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:879)

　　解决方案：arr文件已经封装好了，所以不可能删里面的包。那么只有删除我的相关依赖。根据我的推测是因为项目中有两个相同的包，导致编译的时候不知道使用哪个。最后，干脆哪个都不用。所以找不到方法了。当然这只是我的推测，如果知道真正原因欢迎指出，感激不尽。