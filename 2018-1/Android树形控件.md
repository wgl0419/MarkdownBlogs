#Android树形控件　　


　　
![Aaron Swartz](https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/treeView.gif)　　

　　最近公司需要做一个树形图的效果（就是上面gif图），本来应该是后台传给我已经分好层级的json数据。但是他们所谓的已经分好的层级就是每个元素都有id(结点本身)和pid（父结点）这两个属性。我竟无言以对。因为这两个属性确实分好了层级。

　　下面分析一下实现思路：  

	１.对数据进行排序（处理数据），如下图：

　　
<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/treeViewBlogPic.png"/></div>

　　
	
	　　后台传给我的数据其实是无序的，我需要根据结点的id和pid进行排序。将结点处理成上图所示的顺序。　　
	　　对于数据的处理，我的思路是这样的：　　

	　　　var rootNodes = mutableListOf<NodeBean>()
    　　　var idListMap = mutableMapOf<String, MutableList<String>>()
    　　　var allNodeMap = mutableMapOf<String, NodeBean>()

	　　　１.根结点的pid是没有值的
	　　　２.结点的pid就是父结点的id
	　　　３.对数据进行遍历，如果元素的pid没有值。存入rootNodes集合中
	　　　４.pid有值，在idListMap中使用pid进行查找。即idListMap?.get(pid)
	　　　５.返回的值不为null，说明Map中有这个元素。即得到了idList
	　　　６.将当前结点的id存入idList当中
	　　　７.第4步中返回的是null，则创建一个idList，将当前结点的id存入idList中。然后以当前结点的pid为key，idList为value存入idListMap中
	　　　８.以当前结点的id为key，结点本身（JavaBean）为value存入allNodeMap中
	　　　9.在循环中重复4-8，直到便利完所有的元素

	　　当循环结束之后，我们得到了三样东西：
				
			rootNodes：一个List集合，保存着所有的根节点
			idListMap：一个Map集合,存放着节点的id和该结点所有子节点的id
			allNodeMap：一个Map集合，所有的节点id及其对应的JavaBean

	　　数据已经处理完了，剩下的就是排序了。这里的排序涉及到递归算法。

	　　　 var allNodes: MutableList<NodeBean> = mutableListOf<NodeBean>()

	　　　１.遍历rootNodes
	　　　２.根据根节点的id从allNodeMap中直接get到根节点的JavaBean，放入allNodes中
	　　　３.根据根节点的id在idListMap中 ，直接get到idList。拿到所有子结点的id
	　　　４.遍历idList，根据id从allNodeMap直接get到id对应结点的JavaBean。放入allNodes中
	　　　５.以拿到的子节点为根节点，重复2,3,4操作（递归）。直到找到叶子结点为止。
	　　　６.在第3步中已经进入了递归算法，因为不知道哪个结点才是叶子结点，所以需要用到递归算法