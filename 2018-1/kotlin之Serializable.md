**kotlin之Serializable**
===
>　　activity的界面跳转是Android中最常见的操作了，但是博主最近爬了一个小坑。我们通常使用Intent在activity之间来跳转，并在Intent中添加一些关键数据。带到跳转的activity中进行使用。　　  
>　　博主在使用AndroidStudio的时候，设置的是自动导包，当我想让实体类继承Serializable时出现了下图中的情况。
<br>
<br>
<div align=center><img src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-1/kotlinSerializable1.png"/></div>  
<br>
<br>

>　　大意是无法在kotlin的io包中找到Serializable这个类。看到这个提示有点懵逼，没有Serializable这个类？kotlin不支持？  
>　　Google不可能犯这种错误。但是后来Google了一下发现，kotlin里面确实没有这个类。Serializable是>java.io包中的一个类。我猜，是因为我在kt文件中进行操作。所以AndroidStudio默认只导入kotlin中的包。>知道了问题的原因所在，自然就比较好解决。手动导入java.io包即可。如下图：
<br>
<br>
<div align=center><img src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-1/kotlinSerializable2.png"/></div>