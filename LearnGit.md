### Git学习

Git闯关网站: https://learngitbranching.js.org/?NODEMO=&amp;locale=zh_CN

#### 基础篇

##### 1. Git Commit

Git 仓库中的提交记录保存的是目录下所有文件的快照，非常轻量，可以快速在提交记录之间切换

**提交：**`git commit -m<message>` 

##### 2. Git Branch

Git鼓励早用分支，多用分支

**创建分支：**`git branch <branch-name>`

**切换到分支：**`git switch <branch-name> / git checkout <branch-name> / git checkout <tag>`

**新建并切换到分支：**`git switch -c <branch-name> / git checkout -b <branch-name>`

##### 3. Git Merge

**合并两个分支：**`git merge <branch-name>` <branch-name is the merged branch.> 

##### 4. Git Rebase

第二种合并分支的方法是`git rebase` 。Rebase实际就是取出一系列的提交记录，“复制”它们，然后在另外一个地方逐个的放下去。Rebase优势是可以创建更线性的提交历史。使用 `git rebase <branch-name>` 后，在该branch下，之后提交的记录会重新排列。

`git rebase <branch-name1> <branch-name2>`：将branch-name2合并在branch-name1下方，并且分支移到branch-name2。

#### 高级篇

##### 1. 分离HEAD

HEAD是一个对当前检出记录的符号引用，也就是指向你正在其基础上进行工作的提交记录。HEAD总是指向当前分支上最近一次提交记录，通常情况下指向分支名。

**查看HEAD指向：**`cat.git/HEAD` 

当使用 `git checkout <Hash-Value>`时，HEAD就指向了哈希值代表的具体提交记录，而不是分支名。

##### 2/3. 相对引用(^)(~)

使用提交记录的哈希值很不方便，因此使用相对引用：

**向上移动一个提交记录：** `git checkout <branch-name>^`

**向上移动多个提交记录：** `git checkout <beanch-name>~<number>`

**强制修改分支位置：** `git branch -f <branch-name> HEAD~<number>` <branch-name是被修改的分支，number代表将该branch强制指向HEAD的第number级父提交>。也可以使用 `git branch -f <branch-name> <Hash-Vlaue>` 将该branch强制指向某提交记录的哈希值。

##### 4. 撤销变更

`git reset <branch-name>` 通过把branch记录回退几个提交记录来实现撤销改动，可以想象成“改写历史”，git reset向上移动分支，原来指向的记录就跟从来没有提交过一样。

`git revert <branch-name>` 可以撤销更改并**分享**给别人，它是将要撤销的记录重新复制了一下，添加到现有记录下方

git reset和git revert后面的参数branch-name也可以加 ^ / ~<number> 代表向上撤回的级数。git reset必须加，因为不加的话是撤回本级，没什么意义。

#### 移动提交记录

##### 1. Git Cherry-pick

`git cherry-pick <Hash-Value>` 将一些提交复制到所在的位置(HEAD)下面，cherry-pick是非常简单的方式。哈希值可以是一连串的，每次可以复制多个提交。

##### 2.交互式rebase

交互式rebase指的是带参数 `--interactive` 的rebase命令，简写为 `-i`，如果在命令后增加了这个选项，Git会打开一个UI界面并列出将要被复制到目标分支的备选提交记录，还会显示每个提交记录的哈希值和提交说明，提交说明有助于理解这个提交进行了哪些更改。

当使用 `get reabse -i HEAD^ /HEAD~num` 命令打开rebase UI界面时，可以做3件事：

1. 调整提交记录顺序（通过鼠标拖放来完成）
2. 删除不想要的记录（通过切换 `pick` 的状态来完成，关闭就意味着不要这个提交记录）
3. 合并提交

HEAD^/HEAD~num 代表从哪里开始排序。

#### 杂项

##### 1. 只取一个提交记录

使用 `git rebase -i` 或 `git cherry-pick` 来达到目的。

##### 2/3. 提交的技巧

cherry-pick可以将提交树上任何地方的提交记录取过来追加到HEAD上(只要不是HEAD上游提交的就可以)。

##### 4. Git Tag

`git tag <version-number> <Hash-Value>` 将version-number指向特定哈希值的提交记录，如不设定哈希值，则默认为当前HEAD指向位置。

##### 5. Git Describe

由于标签在代码库中起着“锚点”的作用，Git还为此专门设计了一个命令用来**描述**离你最近的锚点(也就是标签)：

`git describe <ref>`，ref是任何能被git识别成提交记录的引用，默认为HEAD，其输出样式为`<tag>_<numCommits>_g<hash>`，tag是离ref最近的标签，numCommits是表示这个ref与tag相差有多少个提交记录，hash表示的是所给定的ref所表示的提交记录哈希值的前几位。当ref提交记录上有某个标签时，则只输出标签名称。

#### 高级话题

#### Push&Pull - Git远程仓库

##### 1/2. Git Clone

GitHub和Phabricator是远程仓库的可视化操作网站。

#### 3. Git Fetch

`git fetch`完成了仅有但是很重要的两步：

1. 从远程仓库下载本地仓库中缺失的提交记录
2. 更新远程分支指针(如: origin/master)

git fetch实际上将本地仓库中的远程分支更新成了远程仓库相应分支最新的状态。git fetch通常通过互联网(使用http://或git://协议)与远程仓库通信。

##### 4/5. Git Pull

`git pull` ：先抓取更新再合并到本地分支。

`git pull --rebase` 就是fetch和push的简写

##### 6. Git Push

`git push`：负责将**自己的**变更上传到指定的远程仓库，并在远程仓库上合并你的新提交记录。

#### 关于origin和它的周边 - Git远程仓库高级操作

##### 2. 合并远程仓库

rebase的优缺点：

优点：

- rebase使你的提交树变得很干净，所有的提交都在一条线上

缺点：

- rebase修改了提交树的历史

##### 3. 远程追踪

可以让任意分支跟踪origin/master，然后该分支会像master分支一样得到隐含的push目的地以及merge的目标。这意味着你可以在分支totallyNotMaster上执行git push，将工作推送到远程仓库的master分支上。

有两种方法设置这个属性，第一种就是通过远程分支checkout一个新的branch，执行：

`git checkout -b <branch-name> origin/master` 

就可以创建一个名为branch-name的分支，它跟踪远程分支origin/master。

第二种方法是使用 `git branch -u origin/master <branch-name>` 命令，如果当前就在branch-name分支上，就可以省略branch-name。

##### 4/5. Git Push的参数

为push指定参数的语法是：

`git push <remote> <place>` ，此处palce为本地和远程仓库分支名相同时使用，如果不相同，则使用如下语法：

`git push origin <source-branch>:<destination-branch>`

如果没有source-branch参数，则会删除远程仓库的destination-branch分支。

##### 6. Git Fetch的参数

git fetch与git push相似，只不过 source-branch指的是远程仓库，destination-branch指的是本地仓库。但是，不能对正在指向的分支进行操作(题上使用HEAD分离)。

如果没有source-branch参数，则会在本地仓库创建一个destination-branch分支。

##### 7. Git Pull的参数

`git pull origin <source-branch>:<destination-branch>`：从远程仓库source-branch下载分支，并进行合并，然后merge到本地分支。