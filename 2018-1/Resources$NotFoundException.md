Resources$NotFoundException
==========
```java
 android.content.res.Resources$NotFoundException: String resource ID #0x1
	at android.content.res.Resources.getText(Resources.java:386)
	at android.widget.TextView.setText(TextView.java:4200)
	at com.chad.library.adapter.base.BaseViewHolder.setText(BaseViewHolder.java:130)
	at com.shtoone.smarthelmet.adapter.NoticeHistoryAdapter.convert(NoticeHistoryAdapter.kt:57)
	at com.shtoone.smarthelmet.adapter.NoticeHistoryAdapter.convert(NoticeHistoryAdapter.kt:15)
	at com.chad.library.adapter.base.BaseQuickAdapter.onBindViewHolder(BaseQuickAdapter.java:919)
	at com.chad.library.adapter.base.BaseQuickAdapter.onBindViewHolder(BaseQuickAdapter.java:68)
```


TextView 有个setText(int reid) 方法，如果我们从网络上获取到的数据是int 不是String 然后就调用了如下的方法：这个时候不会提示错误，在运行的时候回找不到资源报错。

>解决方法：
>　　使用toString方法变成字符串或者（""+110）

