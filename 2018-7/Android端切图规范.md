Android端切图规范v1.0
==========================
1.容器的概念
------------------
>　　UI设计师在拿到产品经理的原型图后，应该对每张原型图进行分组分类。
>　　具体的分类方法如下:
>　　　　1.每一个功能界面对应一个文件夹
>　　　　2.对原型图中的界面进行整体分割并命名
>　　　　3.基于2中的分割结果创建相应文件夹，分割块中的需要的素材放置到对应文件夹
>　　　　
>　　以医万佳为例，医万佳从整体上可以分为四个大的模块，那么这四个模块就对应四个文件夹（如图）

<div align=center><img src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-7/pic_menu1.png"/></div>　　


　　对首页界面进行分割，从整体上可以分割为三大块。顶部的标题栏，中间的内容展示，底部的导航栏。这三部分可以理解为三个容器，那么就应该在首页文件夹中新建三个对应的文件夹。顶部和底部容器使用的素材较少，所以不再继续分割。

<div align=center><img src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-7/pic_menu2.png"/></div>

　　
>　　主要对内容展示进行分割。内容展示部分又可以分割为5部分（具体如何分割可以咨询Android开发和产品经理），依次为：banner、一级功能、二级功能、在线咨询、医院列表（这里为个人分法仅供参考）。那么就应该在内容文件夹中对应新建五个文件夹。对应容器中的素材放入对应的文件夹。


<div align=center><img src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-7/pic_menu4.png"/></div>


<div align=center><img src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-7/pic_menu3.png"/></div>



2.素材命名
-----------------
>　　分割完所有的界面并新建对应文件夹后，可以将文件目录截图给Android开发。让Android开发尽快给出对应的中英对照文档。
>　　拿到文档结合下面规则就可以愉快的命名素材进行切图了。

###2.1位置+前缀+用途+后缀
>　　这种命名方式会导致图片名称很长，但是便于Android开发人员（以下简称开发）分类和查找。前面说到UI设计师需对图片进行分组放置，但事实上最终所有的素材图片都会被开发放入项目中对应分辨率的mipmap文件夹中（如下图）所有命名是不能重复的。



>　　素材名称只能用英文小写，素材名称中的分隔符必须使用下划线"\_",不能使用其它符号。因为Android编译器只支持下划线。
>　　　　命名用英文小写，分隔符用下划线“\_”!!!
>　　　　命名用英文小写，分隔符用下划线“\_”!!!
>　　　　命名用英文小写，分隔符用下划线“\_”!!!
>　　　　重要的事情说三遍。

<div align=center><img src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-7/pic_menu6.png"/></div>
###2.2.1位置
| 位置  | 说明 | 示例 |
|:----------:|:-----------------------------------|:--------------:|
|top|顶部，多数情况下与标题栏关联使用|top_menu_back（顶部菜单回退）|
|bottom|底部，多数情况下与导航栏关联使用|bottom_menu_refresh（底部菜单刷新）|
|item|条目，一般都是指列表中的条目|item_hospital_list(医院列表条目)|
|home|主页，一般用于主界面相关|home_hospital_list(主界面医院列表)|
|user|用户，一般用于登录用户的数据展示界面|me_head_img(用户头像)|

###2.2.2缩写
| 缩写  | 原文 | 说明 | 示例 |
|:----------:|:-----------------------------------|:--------------|
|ic|icon|主要用于表示布局中的图标或桌面图标|ic_launcher(桌面图标)|
|bg|background|主要用于布局的或控件的背景图片|bg_launcher(启动页图标)|
|btn|button|主要用于按钮相关的图片|btn_delete(删除按钮)|
|img|image|主要表示图片相关|img_banner_1（banner中的第一张图）|
|||||
|||||
|||||

###2.2.3用途
| 用途 | 说明 | 示例 |
|:----------|:-----------------------------------|:--------------|
|tab|选项卡|tab_home(主页选项卡)|
|dialog|对话框|dialog_exit(对话框退出操作)|
|menu|菜单|bottom_menu(底部菜单)|
|circle|圆形、圆圈|bg_circle(圆形背景图)|
|notify|状态栏、通知栏|btn_nofity_download(通知栏下载按钮)|
|pop|弹框(区别于对话框)|bg_pop(弹框背景图)|
|tools|工具栏|tools_tab_home(工具栏中的主界面选项卡)|


###2.2.4后缀
| 用途 | 说明 | 示例 |
|:----------|:-----------------------------------|:--------------|
|normal|默认状态|tab_home_normal(主页选项卡默认状态)，btn_cancel_normal(取消按钮默认状态)|
|selected|选中状态|tab_home_selected(主页选项卡选中状态)|
|pressed|按压状态|btn_cancel_pressed(取消按钮按下时的状态)|
|enabled|不可用|btn_delete_enabled(删除按钮不可用)|


###2.3组合示例
| 命名 | 说明 |
|:----------|:-----------------------------------|:--------------|
|home_top_search_bg|主界面顶部搜索框背景图|
|user_head_img_normal|用户没有上传头像时，展示的默认头像|
|home_bottom_tools_tab_user|主界面底部工具栏用户选项卡|




##3.标注(只使用偶数)

###3.1画布尺寸
>　　做设计图之前须向开发咨询主流屏幕的大小(分辨率)，一般以1920\*1080和1280\*720为主。以该分辨率为画布开发设计图。
>　　UI设计师也可自行查询：<a href="https://mta.qq.com/mta/data/device/resolution">腾讯移动分析</a>

###3.2字体大小
>　　全局标出公用字体即可，特殊字体特殊标注(斜体、加粗、下划线等等)。
>　　Android 4.0之后使用的英文字体是Roboto,中文字体是方正兰亭黑体.
>　　对于xhdpi设备的字号规范:
>　　　　导航标题: 18sp=36px
>　　　　文章标题或图标名称: 16sp=32px
>　　　　文本字体: 14sp=28px
>　　　　注释最小字体: 12sp=24px


|名称|tag|size|
|:--------|:---|:---|
|一级标题|font_size_1|??sp=??px|
|二级标题|font_size_2|??sp=??px|
|三级标题|font_size_3|??sp=??px|
|四级标题|font_size_4|??sp=??px|
>设计师以该表作为参考，标记全局标注


###3.3颜色
>颜色值一般使用十六进制表示，如 #FFFFFF, #90FFFFFF 其中90两位代表透明度


###3.4间距
>间距分为内边距和外边距分别表示为：padding和margin
>当设计图中的文字存在背景但是没有提供背景图时，需要将padding标记出来。
>margin一般不需要标记，但是控件的宽高和控件之间的距离以及容器的高度一定要标记出来。

###3.5控件尺寸
>　　控件尺寸是指,图片在app中展现时的尺寸大小.
>　　设计的时候,一般遵循48dp定律: 48dp作为安卓可触摸的UI元件的标准.
>　　一般来说,48dp转化为一个物理尺寸约9毫米.而建议的目标大小为7-10毫米的范围,这是一个用户手指能准确并且舒适触摸的区域.
>　　如果你设计的元素高和宽至少48dp,就可以保证:
>　　　　(1).触摸目标绝不会比建议的最低目标(7mm)小,无论在什么屏幕上显示.
>　　　　(2).在整体信息密度和触摸目标大小之间取得了一个很好的平衡.
>　　而每个UI元素之间的空白通常是8dp.


###3.6图片资源尺寸
>图片资源尺寸是指,图片资源的大小,可以决定app中图片的清晰度.
>图片资源多为png格式,因为png可以表现透明效果.jpg不能.





|名称|ldpi|mdpi|hdpi|xhdpi|xxhdpi|xxxhdpi|
|:--------|:---|:---|
|程序启动图标|36\*36|48\*48|72\*72|96\*96|144\*144|192\*192|



##4.dp和px关系
1.Android手机有一些初始的分辨率

|名称|ldpi|mdpi|hdpi|xhdpi|xxhdpi|xxxhdpi(4k)|
|:--------|:---|:---|
|密度值|120|160|240|320|480|640|
|分辨率|240\*320|320\*480|480\*800|720\*1080|1280\*1920|2160\*3840|









#注意事项：
##1.切图分包

###1.1APP的桌面图标要切五套：mdpi、hdpi、xhdpi、xxhdpi、xxxhdpi。
>以上面的五种分辨率为名称在launcher文件夹中创建五个文件夹，不同分辨率的桌面图标分别放入对应的文件夹中。图片统一命名为ic_launcher。


#参考资料
><a href="https://material.io/design/introduction/#principles">Material Design(科学上网)</a>
><a href="http://design.1sters.com/#">Material Design 中文版</a>
><a href="https://blog.csdn.net/klxh2009/article/details/74938009">Android UI 切图命名规范、标注规范及单位描述</a>
><a href="https://blog.csdn.net/yuanshuaicsdn/article/details/51878574">Android设计规范-字体字号与图标尺寸</a>
>由于笔者知识及水平有限,因此文中错漏之处在所难免。如发现错漏欢迎联系我校对。
>email：liangfeng093@gmail.com
>qq:544935678


