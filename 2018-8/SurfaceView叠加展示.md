#SurfaceView叠加展示
##项目背景
>　　最近集成网易云信的视频通讯SDK，碰到了一个问题。
>　　画面展示用的是云信封装的SurfaceView，直接在布局中使用。结果绘制画面的时候总是出现覆盖问题。
>　　PS:本地画面小窗展示（覆盖在远端界面之上），远端画面全屏展示。绘制远端画面的时候，本地小窗直接被覆盖。


##解决方案
>　　谷歌了一下“SurfaceView叠加”关键字。很多资料都提到了SurfaceView的setZOrderMediaOverlay(boolean isMediaOverlay)方法。于是去源码中看了一下关于这个方法的描述：
>      * Control whether the surface view's surface is placed on top of another
     * regular surface view in the window (but still behind the window itself).
     * This is typically used to place overlays on top of an underlying media
     * surface view.
     * 
     * <p>Note that this must be set before the surface view's containing
     * window is attached to the window manager.
     *
     * <p>Calling this overrides any previous call to {@link #setZOrderOnTop}.

>大概翻译一下就是：
>>控制窗口表面的view是否处于另一个表面view的上面，执行此api请确定在 layout.addView(thisSurface)之后。
>我在这里的理解是：控制一个SurfaceView是否处于另一个SurfaceView的上面。

>　　那么大致的解决思路就有了：通过addView()方法来添加SurfaceView。而不是直接在布局中直接写SurfaceView。在addView()之后调用setZOrderMediaOverlay()方法来设置是否处于其它view之上(local处于remote之上)。

```java
				// 设置远程视频画布
                var avrRemote = AVChatSurfaceViewRenderer(this@VideoActivity)
                AVChatManager.getInstance().setupRemoteVideoRender(account?.toString(), avrRemote, false, AVChatVideoScalingType.SCALE_ASPECT_FIT)
                fl_remote_container?.addView(avrRemote)
                avrRemote?.setZOrderMediaOverlay(false)

				// 设置本地用户视频画布
	            var avrLocal = AVChatSurfaceViewRenderer(this)
	            AVChatManager.getInstance().setupLocalVideoRender(avrLocal, false, AVChatVideoScalingType.SCALE_ASPECT_FIT)
	            fl_local_container?.addView(avrLocal)
	            avrLocal?.setZOrderMediaOverlay(true)
```

##参考资料
><a href="http://www.it610.com/article/3849172.htm">两个SurfaceView显示预览的问题 setZOrderMediaOverlay(true)</a>
><a href="http://blog.51cto.com/marller/1762028">解决SurfaceView调用setZOrderOnTop(true)遮挡其他控件的问题</a>