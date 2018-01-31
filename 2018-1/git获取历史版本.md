git获取历史版本
=================
　　今天开会，讨论公司的产品。老板检阅了我们最近的工作成果。会议上老板突然提起了一个月之前的一个版本，觉得还不错。想再看看。
　　当时惊了我一身冷汗，因为项目持续推进了一个月。代码早就改的“面目全非”。我的第一反应是赶紧加班重做一版，但是时间上根本来不及。还得想办法，之前学习git的时候。依稀记得git有一个回滚的功能，Google了一下发现可行。
　　我可以将当前的代码备份，然后回退到想要的那个版本。当然目标版本之后的代码会全部消失，我觉得这个操作还是太蠢了。因为回退之后，目标版本之后的commit也会清空。
　　于是又研究了一番，发现git可以根据commit时生成的SHA值，直接拿到目标版本的所有代码。而且不影响当前的代码。
　　具体操作如下：
>　　１．使用gitbash进入git命令行，查看commit记录。操作如下：
```git
git log
```
<br/>
<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-1/git_sha1.png"/></div>

>　　２．找到你想提取的目标版本，复制对应的SHA值。

>　　３．新建一个分支，操作如下：
```git
git branch 新分支名 SHA值
```
<br/>
<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-1/git_new_branch.png"/></div>

>　　４．切换到新的分支，操作如下：

```git
git checkout 新分支名
```
<br/>
<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-1/git_checkout_branch.png"/></div>

　　接下来，IDE(AndroidStudio)中的代码会自动切换成新分支中的代码,也就是历史版本的代码。
　　在上述的操作中，我们新建了一个分支用来拉取历史版本并进行操作（修改代码）。而我们当前的代码，也就是主分支中的代码不受任何影响。


结束语
----------
　　当初学习git的时候，连最基本的add和commit都分不清。而且是为了养成写commit的习惯，以便解决协同开发时产生的冲突。所以没有深入的去学习git，学了点皮毛就开始用。
　　不过最难的就是开始了。最难的一步都踏出去了，还有什么可以阻止我呢？往后的日子里，我将深入学习git作为技术储备。

学习资料
----------
><a href="http://gitbook.liuhui998.com/">Git Community Book</a>
