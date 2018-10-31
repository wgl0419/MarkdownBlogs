#android热更新方案选型


##0.主流的热更新解决方案

<a href="http://www.tinkerpatch.com/Docs/intro">Thinker(微信)</a>
<a href="https://help.aliyun.com/document_detail/51416.html?spm=a2c4g.11186623.2.12.47a3741eTMBlQA">HotFix(阿里云)</a>


##1.什么是热修复？
<div align=center><img src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-10/hot_fix_1.png"/></div>　　



##2.厂商解决方案之间的差异
>　　从左到右依次是：微信、QQ控件、淘宝、美团的热更新的解决方案

<div align=center><img src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-10/hot_fix_0.png"/></div>　　





<div align=center><img src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-10/hot_fix_2.png"/></div>　

> 　　从上图中可以看出微信和阿里云的热更新方案功能是最全面的。
> 　　如果从产品迭代的角度来进行选型，那么毫无疑问使用微信的Thinker(免费)和阿里云的HotFix(收费)。
> 　　一般来说，不建议使用热更新来进行产品迭代。热更新从本质上来说，是进行**替换操作**而不是增加新的东西。根据资料显示如果使用热更新迭代，会引发一系列未知的问题。
> 　　迭代的话还是下载比较合适，使用热更新就行紧急修复即可。





