RecyclerView悬浮效果——StickyHeaderDecoration
=======
前言
-------
>　　悬浮效果之前也做过，但是呢做的很蹩脚。完全是找个博客，把代码一股脑粘到项目里面去。做出来的效果也很丑，有心优化一下。却发现无从下手，因为对原理一点也不懂。
>　　这次闲来无事，找到两篇写的比较好的博客（文末会贴出链接）。把原理完全弄懂了，在这里记录一下。

##１.ItemDecoration
>　　关于ItemDecoration，大家可能更多的是去用它去画item的分割线。事实上它也可以实现我们想要的悬浮效果。
>　　下面列出我们比较常用的几个方法：

 * getItemOffsets：通过Rect为每个Item设置偏移，用于绘制Decoration
 * onDraw：通过该方法，在Canvas上绘制内容，在绘制Item之前调用。（如果没有通过getItemOffsets设置偏移的话，Item的内容会将其覆盖）
 * onDrawOver：通过该方法，在Canvas上绘制内容,在Item之后调用。(画的内容会覆盖在item的上层)

下面放出部分源码：

```java
 public static abstract class ItemDecoration {
    ...
    public void onDraw(Canvas c, RecyclerView parent, State state) {
        onDraw(c, parent);
    }
    public void onDrawOver(Canvas c, RecyclerView parent, State state) {
        onDrawOver(c, parent);
    }
    public void getItemOffsets(Rect outRect, View view, RecyclerView parent, State state) {
        getItemOffsets(outRect, ((LayoutParams) view.getLayoutParams()).getViewLayoutPosition(),
                parent);
    }
}
```

>　　下面这张图是理解悬浮效果的关键，一定一定要看懂。
>　　这里要感谢这张图的作者<a href="https://www.jianshu.com/u/769d3d3a9d4b">带心情去旅行</a>
>　　正是这张图帮我理解了悬浮效果的原理




2.为悬浮的item预留空间
------
>　　首先要找出每个分组中的第一个元素，代码如下：

```java
	/**
     * 通过与上一个item对比，判读是否为同一组
     * 不相同，当前元素则为另一分组的第一个元素
     */
    fun isFirstInGroup(position: Int): Boolean {
        if (position == 0) {
            return true
        } else {
            var previousGroupName = getGroupName(position - 1)
            var currentGroupName = getGroupName(position)
            return !TextUtils.equals(previousGroupName, currentGroupName)
        }
    }
```

>　　在每个分组的第一个item的上面，使用getItemOffsets()方法让item发生偏移。留出悬浮栏的位置

```java
	//为悬浮栏预留空间
    override fun getItemOffsets(outRect: Rect, view: View, parent: RecyclerView, state: RecyclerView.State) {
        super.getItemOffsets(outRect, view, parent, state)
        var position = parent.getChildAdapterPosition(view)//获取当前item的位置
        var groupName = getGroupName(position)
        //只有是同一组的第一个才显示悬浮栏
        if (position == 0 || isFirstInGroup(position)) {
            outRect.top = mHeaderHeight.toInt()
        }
    }
```


3.绘制分组头部
---------
>　　使用onDrawOver()来为每一个分组绘制头部。从上面的图中可以看到，onDrawOver()绘制的东西和item不在同一层。所以，我们绘制出来的头部是覆盖在item(那一层)的上面(一层)。而不是绘制在item的上面(top),也就是说我们绘制的头部只是刚好盖在预留出来的位置上。这里有点绕，大家根据图片来理解。
>　　这里还需要计算一下位置(重点理解)：
* 先获取当前屏幕所有recyclerView显示的item 
* 分组头部(bottom)距离顶部==2*headerHeight时，悬浮头部就要向上(开始)偏移（上推效果）
* 分组头部(bottom)距离顶部==headerHeight时，悬浮头部偏移headerHeight（推离屏幕效果） 

```java
override fun onDrawOver(c: Canvas, parent: RecyclerView, state: RecyclerView.State) {
        super.onDrawOver(c, parent, state)

        //向上取整  计算一个字符串宽度
        var sigleCharWidth = Math.ceil(Layout.getDesiredWidth("0", mTextPaint).toDouble())

        var topOffset = 0f//绘制悬浮头部的偏移量
        var firstGroupName = ""
        var itemCount = state.itemCount
        var childCount = parent.childCount//可见item数量
        //位置坐标,不是长度
        var left = parent.left + parent.paddingLeft
        var right = parent.right - parent.paddingRight

        //文字基线到中轴线的距离(y轴)
        var fontMetrics = mTextPaint?.fontMetrics
        var baseLineDistance = (fontMetrics?.descent!! - fontMetrics?.ascent!!) / 2 - fontMetrics?.descent!!
        /*for循环里面绘制每个分组的头部*/
        for (index in 0..childCount - 1) {
            var childView = parent.getChildAt(index)
            //获取当前view在Adapter里的position
            var position = parent.getChildAdapterPosition(childView)
            var groupName = getGroupName(position)
            if (index == 0) {
                firstGroupName = groupName
            }
            //位置坐标,不是长度
            var viewTop = childView.top + parent.top//始终为距离x轴的距离坐标
            if (position == 0 || isFirstInGroup(position)) {//如果当前位置为0或者是分组中的第一个
                //绘制矩形
                c.drawRect(left.toFloat(), (viewTop - mHeaderHeight).toFloat(), right.toFloat(), viewTop.toFloat(), mRectPaint)
                //注意文字基线的位置
                c.drawText(groupName, ((right - left) / 2).toFloat(), (viewTop - mHeaderHeight / 2 + baseLineDistance).toFloat(), mTextPaint)
//                c.drawLine(((right - left) / 2).toFloat(), 0f, ((right - left) / 2).toFloat(), mHeaderHeight, mTextPaint);//画条线看看文字居中不
                if (mHeaderHeight < viewTop && viewTop < 2 * mHeaderHeight) {//距离坐标位于一个高度和两个高度直接
                    topOffset = (viewTop - 2 * mHeaderHeight).toFloat()
                }
            }
        }

        c.save()//保存已经绘制的部分
        c.translate(0f, topOffset)//移动画笔的位置

        //绘制悬浮头部
        c.drawRect(left.toFloat(), 0f, right.toFloat(), mHeaderHeight, mRectPaint)
        //注意文字基线的位置
        //居中显示
        c.drawText(firstGroupName, ((right - left) / 2).toFloat(), mHeaderHeight / 2 + baseLineDistance, mTextPaint)
//      canvas.drawLine(0, headerHeight / 2, right, headerHeight / 2, mHeaderTxtPaint);//画条线看看文字居中不
//        c.drawLine(((right - left) / 2).toFloat(), 0f, ((right - left) / 2).toFloat(), mHeaderHeight, mTextPaint);//画条线看看文字居中不
        c.restore()
    }


```

结语
--------
>　　尽可能的手动实现一些常见的效果或者交互，很多其实都不复杂。理解之后很简单，别人的框架终究不是最适合自己的。
>　　最后还是要感谢两位大佬的文章，帮我理解原理。

参考资料
--------
<a href="https://blog.csdn.net/qian520ao/article/details/76167193">RecyclerView 悬浮/粘性头部——StickyHeaderDecoration</a>
<a href="https://www.jianshu.com/p/b335b620af39">【Android】RecyclerView：打造悬浮效果</a>


