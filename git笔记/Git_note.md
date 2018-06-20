# Git分布式版本控制系统：
学习[廖雪峰的git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b00)  笔记( 图片转自[廖雪峰](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b00) ）。
### 一、简介
* 随时掌握工作区的状态，使用`git status`命令。
* 如果`git status` 告诉你有文件被修改过，可以用`git diff` 查看修改内容。
* 在`git`中进行修改后可以保存一个快照，称为`commit`，如果文件被删除，可以通过最近一个`commit`恢复。
* 使用`git log`命令来查看历史记录。 
    * `--pretty=oneline`参数简要输出相关信息。
* 回退到上一个版本：
    * 在`git`中，`HEAD`表示当前版本，上一个版本就是`HEAD^`,上上个版本就是`HEAD^^`。（上100个版本`HEAD~100`。
    * 把当前版本回退分层上一个版本，使用`git reset --hard <HEAD^>`命令。
    * 回到未来的版本，使用`git reset --hard <commit ID>`(前提是命令行窗口未关闭，版本号可以写前几位即可）
    * `git reflog`可以记录操作的每一条命令。

### 二、工作区和暂存区
* 工作区（working directory）
创建的文件夹就是一个工作区。
* 版本库（Repository）：
工作区中隐藏的目录`.git`是Git的版本库。
版本库中存有（隐藏目录）：
    * stage（index）暂存区
    * Git自动创建的第一个分支：`master`.
    * 指向`master`的一个指针`HEAD`。
    * 在往Git版本库中添加文件时，分两步执行：
        * 1、用`git add`把文件加进去，实际上就是把文件修改添加到暂存区；
        * 2、用`git commit`提交更改，实际上就是把暂存处的所有内容提交到当前分支`master`。

**需要提交的文件修改后通通放到暂存区，然后，一次性提交暂存区的所有修改。**

### 三、管理修改
* `Git`跟踪并管理的是修改，而非文件。  
每次修改文件过后，如果不用`git add`将文件提交到暂存区，同样的文件也不会加入到`commit`中。  
#### 1、撤销更改  
在`git add` 之前发现文件修改有错，可以使用`git checkout -- [filename]`丢弃*工作区*的修改。  
**命令`git checkout -- readme.txt`有两种情况：  
1、文件`readme.txt`自修改后还没有被放到暂存区，撤销修改就回到和版本库一模一样的状态。（还未使用`git add`）  
2、文件已经被添加到暂存区后，又做了修改，撤销修改就回到添加到暂存区后的状态。（已经使用了`git add`后对文件进行了修改）  
也就是让文件回到最近一次`git commit`或`git add`状态**    
*如果`git checkout -- file`命令没有使用`--`，就会变成“切换到另一个分支”的命令。*  

* 在修改文件并使用了`git add`将文件放到了暂存区后，可以使用`git reset HEAD <file>`命令可以把*暂存区*的修改撤销掉（unstage），重新将文件放回工作区。

### 四、删除文件
当需要删除已给文件时，在工作区将文件删除后，需要从版本库删除文件`git rm <file>`。然后使用`git commit -m ""`进行提交，这样工作区和版本库都将该文件删除了。  
如果在工作区中删错了文件，可以使用`git checkout -- <file>`将误删的文件恢复到最新版本*实际上该命令是用版本库里的版本替换工作区的版本*。

### 五、远程仓库

* 关联`github`：  
1. 创建SSH Key：使用`ssh-keygen -t rsa -C "youremail@example.com"`创建秘钥。
2. 进入`.shh`文件夹复制`id_rsa.pub`公钥中的内容到Github中的`Add SSH Key`中。
*如果有多台电脑，则可以在每一台电脑中生成一个新的Key并添加到github中。*
* 添加远程库：  
    * 在github上新建一个仓库后，同本地已有的仓库关联，然后把本地仓库推送到Github库：使用`git remote add origin https://github.com/soufal/my_git.git`，这样添加后的远程库的名字就为`origin`，是git的默认叫法。
* 推送：
    * 使用`git push -u origin master`把本地库的所有内容推送到远程库。
    **这里使用加`-u`的命令是因为第一次推送`master`分支时，Git不但会吧本地的`master`分支内容推送到远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或拉取时就可以简化命令了。**
* 从远程库克隆
    * 可以现在Github上创建一个远程库`new`，再从远程库克隆到本地。使用`git clone https://github.com/soufal/new.git`或者`git clone git@github.com:soufal/new.git`。
    * 两者的不同在于：前者使用的是`https`协议，速度较慢，每次推送都需要输入口令。后者使用的是`SSH`协议，其速度较快。
    * 
### 六、分支管理
可以创建一个属于自己的分支，其他人看不到，还可以在原来的分支上继续工作，自己可以在自己的分支上继续工作。需要提交后再提交，可以一次性合并到原来的分支上。

#### 1、创建于合并分支：
在`Git`中，每一次的提交，Git都会把这些提交串成一条时间线，这条时间线就是一个分支。一直在使用的默认分支叫做主分支。即`master`分支。`HEAD`严格来说是指向`master`的，`master`才是指向提交的。所以可以说：*`HEAD`指向的就是当前分支。*
一开始，`master`分支就是一条线，Git用`master`指向最新的提交，再用`HEAD`指向`master`，就可以确定当前分支以及当前分支的提交点：
![image1](https://cdn.liaoxuefeng.com/cdn/files/attachments/0013849087937492135fbf4bbd24dfcbc18349a8a59d36d000/0)

当创建新的分支时`dev`时，git新建一个指针叫做`dev`，指向`master`相同的提交，再把`HEAD`指向`dev`，就表示当前分支在`dev`上：
![image2](https://cdn.liaoxuefeng.com/cdn/files/attachments/001384908811773187a597e2d844eefb11f5cf5d56135ca000/0)

因此可以看出git创建一个分支很快，只需要增加一个指标`dev`，并修改`HEAD`的指向即可。修改过后，对工作区的修改和提交就只是针对`dev`分支了，而`master`指针不变：

![image3](https://cdn.liaoxuefeng.com/cdn/files/attachments/0013849088235627813efe7649b4f008900e5365bb72323000/0)

如果在`dev`上的工作完成了，就可以将`dev`合并回`master`上。可以直接将`master`指向`dev`的当前提交（正如最开始创建分支一样），就完成了合并：

![image4](https://cdn.liaoxuefeng.com/cdn/files/attachments/00138490883510324231a837e5d4aee844d3e4692ba50f5000/0)

合并同样也很快，也只需要改指针。
合并完成后，可以删除`dev`分支，也就是把`dev`指针删除，删除后就值剩下一个`master`分支。
![image](https://cdn.liaoxuefeng.com/cdn/files/attachments/001384908867187c83ca970bf0f46efa19badad99c40235000/0)


#### 2、相关命令：
* 创建`dev`：  
`git checkout -b dev`
`git checkout`命令加上`-b`参数表示创建并切换。相当于:  

```
git branch dev  
git checkout dev
```
* 可以使用`git branch`查看当前分支。**会列出所有的分支，当前分支前有一个`*`**
当完成工作提交后，通过`git checkout master`命令返回`master`分支。  
然后使用`git merge dev`把`dev`上的工作合并到`master`上。  
**该指令用于合并指定分支到当前分支。**  
* 合并完成后使用`git branch -d dev`删除`dev`分支。  
* 使用`git log`可以查看分支合并情况。
* 使用`git log --graph`乐意查看分支合并图。

#### 3、分支管理策略
* 合并分支时，Git会用`Fast forward`模式，在这种模式下，删除分支后，会丢掉分支信息。  
* 使用`--no-ff`方式的`git merge`会强制禁用`Fast forward`，这样git会在`merge`时生成一个新的`commit`，这样可以从分支历史上看出分支信息。 （*创建新的`commit`可以用过加上`-m`参数实现*） 

----------
在实际开发中，管理分支的几种原则：
* `master`分支应该是稳定的，也是仅仅用来发布新版本，平时不在其上进行工作。
* 工作都在`dev`分支上，因为`dev`是不稳定的。
* 可以在`dev`上再创建新的分支，和别人协同工作，最后再合并到`dev`上。如下图所示：
![image5](https://cdn.liaoxuefeng.com/cdn/files/attachments/001384909239390d355eb07d9d64305b6322aaf4edac1e3000/0)

--------------
 * Bug分支：  
 Git提供了一个`stash`功能，可以把当前工作现场“储存”起来，等当前临时工作做完后再恢复现场继续工作：  `git stash`。
* 确定bug需要在哪个分支上修复，就在那个分支上创建新的临时分支。
* 恢复工作区：使用`git stash list`查看之前隐藏的工作区。恢复方法有两种:
    * 使用`git stash apply`恢复，但是恢复后，stash的内容并不删除，需要再使用`git stash pop`来删除。
    * 使用`git stash pop`，恢复的同时会把stash的内容也删了。
    * 可以多次`stash`，使用`git stash list`查看，然后恢复指定的stash。
    使用`git stash apply stash@ {id}`。

* Feature分支：  
需要给当前软件添加新功能时，最后新建一个feature分支，在上面开发，完成之后，再合并分支。  
在新分支上完成工作后，合并前临时需要丢弃删除分支feature，不需要进行合并。
这时只能够使用`git branch -D <name>`强行删除分支。

#### 4、多人协作  
当从远程库克隆时，实际上Git自动地把本地的`master`分支和远程的`master`分支对应起来了，并且，远程仓库的默认名称是`origin`。  
查看远程库信息：`git remote`。  
使用`git remote -v`查看更详细的信息。
* 推送分支：就是该分支上的所有本地文件提交推送到远程库。`git push origin master`。（将本地的`master`分支推送到远程的`origin`分支。）
* `master`主分支，因此要随时与远程同步。
* 抓取分支(从别人的远程库中克隆）：`git clone git@github.com:username/gitname`
* 若不同的主机向同一个分支提交了对同一文件的修改，则会造成冲突：使用`git pull`把最新的提交从远程库中抓下来，然后在本地合并，解决冲突，再推送提交。在这个过程中，如果出现`no tracking information`错误，则需要指定本地当前分支与远程相应分支的链接，使用`git branch --set-upstream-to=origin/dev dev`后再进行`pull`。
----------------------------
* Rebase：把分支中分叉的提交变成直线。  
    * 使用`git rebase`命令把分叉的提交历史整理成一条直线，看上去更直观，缺点是本地的分叉提交已经被修改过了。

#### 5、标签管理  
标签可以看做是版本库的快照，实际上就是指向某个`commit`的指针，（与分支的不同在于：标签不能移动）。  
* 创建标签：在需要打标签的分支上：`git tag <name>`
* 使用`git tag`查看所有标签。
* 默认的标签是打在最新提交的`commit`上的。若要给之前的`commit`打标签，只需找到历史提交的`commit id`:使用`git log --pretty=oneline --abbrev-commit`。找到`id`后使用`git tag <tagname> <commitid>`来打标签。
* 使用`git show <tagname>`查看标签信息。
* 创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：`git tag -a <tagname> -m "说明" <commitid>` 
**若某个`commit`既出现在`master`分支，又出现在`dev`分支，那么这两个分支上都可以看到标签。**  
* 删除标签：`git tag -d <tagname>`。
* 推送标签到远程：`git push origin <tagname>`。
* 使用`git push origin --tags`一次性推送到远程。
* 删除已经推送到远程的标签：先删除本地，再使用`git push origin :refs/tags/<tagname>`。