#RecyclerView.Adapter深入学习

##项目背景
>　　最近要做即时通讯的聊天界面，公司不让用SDK厂商提供的UI包。必须自定义。那么Adapter必须自己封装才能做出合适的效果。之前的所有RecyclerView列表我都用的<a href="https://github.com/CymChad/BaseRecyclerViewAdapterHelper">BaseRecyclerViewAdapterHelper</a>，非常方便。但是，都是基于每个item的样式相同做的复用封装。用到聊天界面的消息列表明显不合适。
>　　我的思路是在item中左右显示隐藏来展示消息列表。

##源码介绍
>　　直接Ctrl看Adapter的源码，第一页会看到下面的英文介绍：
	 * Base class for an Adapter
     *
     * <p>Adapters provide a binding from an app-specific data set to views that are displayed
     * within a {@link RecyclerView}.</p>
     * @param <VH> A class that extends ViewHolder that will be used by the adapter.

>　　适配器提供从特定的应用程序数据集到RecyclerView中显示的视图绑定。（简单的理解就是：提供数据和控件的绑定）
>　　