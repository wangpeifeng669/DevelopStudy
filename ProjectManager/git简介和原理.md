git简介和原理
===

###（一）简介
Git是一款免费、开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。  

###（二）历史
Linus创建了开源linux之后，尝试用BitMover公司的BitKeeper做代码管理，后来闹翻，Linus自己设计和开发了Git来代替BitKeeper。  
不久，Git 迅速席卷整个开源社区，以Git起家的Github轻松挤垮了SVN阵营的Google Code。

###（三）原理
####（1）工作方式
git是分布式的，跟集中式的svn不同。开发者可以提交到本地，每个开发者通过克隆（git clone），在本地机器上拷贝一个完整的Git仓库。提交的时候是先提交到本地，本地还可以提交到其他的服务器。  
####（2）工作区和暂存区
#####1. 工作区
电脑上的项目目录就是工作区。
#####2. 暂存区
在工作区中有隐藏目录.git，存放所有的工作区变更信息，其中包括stage暂存区。  
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/ProjectManager/pic/git%E7%AE%80%E4%BB%8B%E5%92%8C%E5%8E%9F%E7%90%861.jpeg?raw=true)  
在工作区中修改文件，通过git add添加修改到暂存区，再通过git commit将暂存区中的改动添加到mater分支。最后再同步本地mater分支和服务器端的master分支，将修改提交到服务器。  
####（3）分支合并
#####Fast-forward“快进模式”
**开始状态**  
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/ProjectManager/pic/git%E7%AE%80%E4%BB%8B%E5%92%8C%E5%8E%9F%E7%90%862.png?raw=true)  

**创建分支**  
创建dev分支，此时修改指针HEAD指向新的dev分支。
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/ProjectManager/pic/git%E7%AE%80%E4%BB%8B%E5%92%8C%E5%8E%9F%E7%90%863.png?raw=true)  

**分支改动**  
分支dev上修改了文件信息。
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/ProjectManager/pic/git%E7%AE%80%E4%BB%8B%E5%92%8C%E5%8E%9F%E7%90%864.png?raw=true)  

**合并分支**  
将dev上的改动同步到master分支，只需要将master指针移动到dev位置，HEAD指向master。
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/ProjectManager/pic/git%E7%AE%80%E4%BB%8B%E5%92%8C%E5%8E%9F%E7%90%865.png?raw=true)  

**最终状态**  
最后可以选择清除dev分支的痕迹。
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/ProjectManager/pic/git%E7%AE%80%E4%BB%8B%E5%92%8C%E5%8E%9F%E7%90%866.png?raw=true)  
Fast-forward“快进模式”只是其中一种同步模式，也可以选择其他方式合并。  

详细的git用法参加[网上教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)。