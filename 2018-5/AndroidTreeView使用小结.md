**AndroidTreeView使用小结**
=========

><a href="https://github.com/bmelnychuk/AndroidTreeView">AndroidTreeView</a>

1.配置gradle
```kotlin
	compile 'com.github.bmelnychuk:atv:1.2.+'
```
2.初始化AndroidTreeView
```kotlin
	treeView = AndroidTreeView(this, root)
        treeView?.setDefaultAnimation(true)
        treeView?.setDefaultViewHolder(IconTreeItemHolder::class.java)

        treeView?.setDefaultNodeClickListener(object : TreeNode.TreeNodeClickListener {
            override fun onClick(node: TreeNode?, any: Any?) {
                if (!node?.isExpanded!!) {
                    node?.viewHolder?.view?.findViewById<ImageView>(R.id.ivArrow)?.setImageResource(R.mipmap.item_arrow_down)
                } else {
                    node?.viewHolder?.view?.findViewById<ImageView>(R.id.ivArrow)?.setImageResource(R.mipmap.item_arrow)
                }
            }
        })
```
3.数据处理
<div align=center><img src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-5/TreeView.png"/></div>
```kotlin
	/**
     * rootNodes(list集合)
     * Map<id,talkList<String>>  list是子节点id集合
     */
    var idListMap = mutableMapOf<String, MutableList<String>>()
    var allNodeMap = mutableMapOf<String, NodeBean>()
    var root: TreeNode = TreeNode.root()

    fun dealWithData(allObjs: MutableList<OrgNode>) {
        Observable.fromIterable(allObjs)
                .flatMap(object : Function<OrgNode, Observable<NodeBean>> {
                    //使用flatMap处理数据
                    override fun apply(curObj: OrgNode): Observable<NodeBean> {
                        var node = NodeBean()
                        node?.realname = curObj?.name
                        node?.pid = curObj?.pid
                        node?.id = curObj?.id
                        node?.childCount = curObj?.cnt
                        node?.type = curObj?.type
                        if ("1".equals(curObj?.type))
                            node?.isMember = true
                        else
                            node?.isMember = false
                        return Observable.just(node)
                    }
                })
                .subscribe(object : Consumer<NodeBean> {
                    override fun accept(node: NodeBean) {
                        if (node?.pid?.equals("0")!!) {//node为根节点的子节点
                            node?.level = 0
                            if (idListMap?.get(node?.pid!!) == null) {//根节点在node后面
                                var cIds1 = mutableListOf<String>()//创建子节点id集合
                                cIds1?.add(node?.id!!)//当前节点id添加到集合
                                idListMap?.put(node?.pid!!, cIds1)
                                allNodeMap?.put(node?.id!!, node)
                            }
                            if (idListMap?.get(node?.id!!) == null) {//当前节点idListMap中不存在
                                var cIds2 = mutableListOf<String>()
                                idListMap?.put(node?.id!!, cIds2)
                                allNodeMap?.put(node?.id!!, node)
                            }
                        } else {//根节点有pid
                            var pid = node?.pid
                            var cIds = idListMap?.get(pid)
                            if (cIds != null) {//
                                cIds?.add(node?.id!!)
                            } else {
                                var cIds = mutableListOf<String>()
                                cIds?.add(node?.id!!)//子节点id集合
                                idListMap?.put(node?.pid!!, cIds)//保存id和子节点的关系
                            }
                            allNodeMap?.put(node?.id!!, node)
                        }
                    }
                })
    }

    /**
     * 递归添加节点
     * @param pNode 父节点
     * @param id 节点id
     */
    fun showNode(pNode: TreeNode, id: String) {
        idListMap?.get(id)?.forEach {
            var nodeBean = allNodeMap?.get(it)
            var node = TreeNode(IconTreeItemHolder.IconTreeItem(nodeBean?.realname!!, nodeBean?.id, nodeBean?.name, nodeBean?.pid, nodeBean?.childCount!!, nodeBean?.type!!))//当前节点
            pNode?.addChild(node)
            showNode(node, it)
        }
    }
```

4.绘制树形图
```kotlin
	 	//0是根节点id
        showNode(root, "0")//递归绘制树形图
        container?.addView(treeView?.view)//将树形图添加到父容器
```

><a href="https://github.com/liangfeng093/AndroidTreeViewDemo">demo代码</a>