# 版本管理
### SVN的使用
项目经理对项目进行初始化工作（比方说，仓库有Code和Doc 两个文件夹）
##### 1、 创建文件
```objc
1、项目经理需要连接远程服务器的SVN仓库，把仓库下载到自己的电脑上
svn checkout https://192.168.181.145/svn/project/ --username=jackey --password=jackey
注意 .svn 隐藏文件夹就是SVN的版本文件夹

2、切换到code路径中，创建Main.m文件
touch Main.m

3、查看本地仓库的状态
svn status
? 表示该文件没有被SVN管理

4、把新创建的文件添加到SVN的管理者, 然后查看文件的状态
svn add Main.m
svn status
A 表示该文件被添加到SVN

5、把本地仓库的更改（包含创建的文件）提交到远程仓库
svn commit -m"创建了Main.m文件"。 //这里是添加的提交文件的注释，为什么提交这次代码，每次提交都要添加注释

6、终端中直接向文件添加内容，修改文件,并查看文件的状态
echo "Main.m文件将被修改" > Main.m
svn status
M 表示该文件被修改

7、把本地的更改提交到远程的服务器
svn commit -m"修改了Main.m文件，添加了内容"

```

##### 2、删除文件
```objc
1、项目经理删除Main.m文件，并查看状态
svn remove Main.m
svn status
D表示该文件已经被删除

2、把本地的更改（删除文件）提交到远程的服务器
svn commit -m"删除了文件" Main.m
```

##### 注意，提交的时候，后面可以跟具体的文件，也可以不跟，跟具体文件，表示只提交该文件，不跟具体文件，表示要将所有的更改的文件都进行提交操作

##### 3、SVN相关命令的简写
```objc
checkout  			--> co
status     			--> st
commit    			--> ci
remove.   			--> rm
update.    			--> up
```

##### 4、查看版本信息
```objc
1、首先要更新获取最新的仓库的信息
svn update 

2、查看仓库的版本信息
svn log
```

##### 5、新同事张三加入公司开发涉及操作
```objc
1、张三加入公司开发，分配SVN账号，账号==秘密==zhangsan,并设置权限

2、张三连接到远程的服务器，把仓库下载到本地
svn checkout https://192.168.181.145/svn/project/ --username=zhangsan --password= zhangsan

3、张三切换到Code目录下，创建Dog.h Dog.m文件
// 先切换目录
touch Dog.h Dog.m

4、查看文件的状态（？注意新创建的文件默认是不会被SVN管理的）
svn status

5、把新创建的文件纳入到SVN管理当中
svn add Dog.h Dog.m 或者
svn add * （表示把当前的文件路径中的所有的文件都添到SVN版本库中）

6、把本地的更改提交到远程的仓库（这里可以不写具体的文件名称）
svn commit -m "创建了狗类" Dog.h Dog.m

7、其他同事更新获取新代码
svn update

8、以此内推
```

##### 6、多人开发的时候可能会产生冲突
```objc
场景：
1、假设项目经理往Dog.h文件中修改了内容，并把修改提交到远程的仓库
svn commit -m "项目经理修改了第二行，写了我是项目经理这个字符串" Dog.h

2、然后张三并没有及时更新代码，然后修改了同一个地方的内容并提交
svn commit -m "我是张三修改了第二行，写了我是张三这个字符串" Dog.h

会产生一个错误 ==error== is out of date 该文件已过期：当前的版本低于服务器的版本，无法提交成功

3、张三需要先更新获取最新的仓库信息
svn update  // 但是这个操作会产生冲突，因为服务器的代码和本地的自己的代码不一致

比方说Dog.h文件的内容如下
hello Dog
+<<<<<<<<<< .mine
+你好，我是张三！
+=========
+你好，我是项目经理
+>>>>>>>>>> .r10
Select: (p)postpone, (df)diff-full, (e)edit, (r)resolved,
	     (mc)mine-conflict, (tc)-theirs-conflict,
	     (s)show all options:mc

.mine 表示是我的内容， .r10表示的是服务器当前的版本内容
(p)postpone: 表示稍后处理
(mc)mine-conflict:表示以我的为准，用我的数据覆盖服务器的数据
(tc)theirs-conflict：表示以他们的为准， 用服务器的数据覆盖我的数

选择p:手动解决冲突的步骤
1、打开有冲突的文件，把冲突解决（删除特殊字符）
2、通过命令行告诉SVN已经手动的把冲突解决了：svn resolved Dog.h
3、把更改提交到远程仓库： svn commit -m "更改了冲突，使用p解决的" Dog.h
```


##### 7、版本的回退（revert｜merge）（回退与合并操作）
```objc
版本的回退的两种情况
1、本地的代码做了更改，该操作还没有提交到远程仓库，取消更改
svn revert Dog.h

2、本地的代码做了修改，该修改已经被提交到了远程仓库，回退到上一个版本
	2.1、回到指定的版本 svn update -r版本号
	eg:svn update -r10
	
	2.2、版本回退
		2.2.1、先更新获得最新的版本
		svn update
		2.2.2、用指定的版本的内容来覆盖当前的版本的内容
		svn merge -r15:r14 Dog.h	 // 表示使用第14个版本的Dog.h文件的内容来覆盖第15个版本的内容
		2.2.3、还需要把更改提交到远程仓库
		svn commit -m "Dog.h文件要回腿到上一个版本"

```

##### 使用图形化工具进行项目初始化的配置
```objc
1、使用Xcode创建一个新的项目放在本地的仓库中，新项目创建的时候，默认会自动做一次add添加操作；
2、使用图形化工具提交代码到远程的服务器，选择commit按钮，之后会弹出一个提示框，检测到要忽略的文件，应该如何处理呢？
3、选择ignore，点击右下角的commit changes
4、继续处理忽略文件

设置仓库的忽略文件
1、哪些文件是需要忽略的【2个文件】userdata保存断点信息以及目录展开情况
2、首先要把需要忽略的文件userdata删除，commit提交更改到服务器（需要写注释-commit changes）
3、要重新生成这两个文件（使用XCode把项目打开，在项目中随便添加一个断点信息，随便修改一行代码，然后编译就行）
4、手动忽略这两个文件，两种方式
	4.1、直接commit提交，选择ignore
	4.2、分别设置两个文件的忽略，然后提交（选中文件，右键即可）
5、完成忽略之后，提交（commit）会报错（有冲突）
6、先更新，再提交
7、验证忽略操作是否完成
```

##### SVN目录规范介绍
```objc
Trunk：主干
Tags：备份
branches：分支
场景：
1、某开发团队，开始开发一款App MoMo，命名为MoMo1.0版本，发布到AppStore+备份
2、因为市场反馈良好，因此该团队决定开发2.0版本
3、当开发团队开发2.0的途中，这时市场人员反馈已经上架的1.0版本中存在重大Bug，需要马上修复；
4、开发团队指派固定的人员去修复1.0版本中的Bug，并完成上线替换
5、被指派的人员，应该根据之前的备份的1.0版本来创建一个分支，并在分支中修复
6、当分支中的Bug修复完成之后，再把版本命名为1.1版本，发布到App Store + 备份
7、在主干上合并分支，解决当前正在开发的2.0版本中存在的同样的Bug
8、删除分支，继续开发2.0版本
```


### Git的使用
```objc
git 和SVN的对比
1、在很多情况下，git的速度是远远的快于SVN的速度；
2、SVN是集中式管理，Git是分布式管理；
	eg. SVN相当于一个公司的总经理，手下有三个员工，需要总经理管理，后来扩展为30人，但是还只能总经理管理，总经理分配任务，假如总经理不来上班，所有员工的工作没有人分配工作；而Git就相当于，扩招后可以分配三个团队，每个团队一个经理，然后总经理管经理，经理管员工，层级关系，总经理不来上班，经理也可以分配工作，还能正常的运行。
3、SVN使用分支比较笨拙，Git可以轻松的拥有无限个分支；
4、SVN必须联网才能正常工作，Git支持本地版本控制工作；
```

##### git初始化和访问配置
```objc
1、初始化一个本地仓库，在桌面新建一个空的文件夹Git，不要含有中文，然后终端里面，切换到文件夹，初始化
git init

2、配置仓库的用户名和邮箱
git config user.name "jackey"
git config user.email "yanrenjie1114@163.com"
这个配置的是单个仓库的

3、配置全局Git仓库的用户名和邮箱（Git会先查找当前仓库的用户名和邮箱，如果没有，然后查找全局的用户名和邮箱）
git config --global user.name "jackey"
git config --global user.email "yanrenjie1114@163.com"
```
##### git仓库项目初始化操作
```objc
1、首先初始化一个Main.m文件
touch Main.m

2、查看版本库中文件的状态
git status 
// 红色，表示文件没有添加到Git的赞缓存

3、把创建的文件添加到暂缓区中
git add Main.m
// 绿色，表示文件已经提交到暂缓区

4、把暂缓区中的更改提交到本地版本库
git commit -m "新建了文件Main文件" Main.m

5、修改了文件Main.m，重新查看文件的状态
echo "hello, world" > Main
git status //红色，需要添加到暂缓区

6、把文件的修改操作添加到暂缓区
git add Main.m

7、把更改提交到本地的版本库
git commit -m "修改了文件内容" Main.m

8、
```

#### 注意：在SVN中，只有新创建的文件才需要做add操作，但是在Git中不一样，新创建的文件或者修改了已经创建的文件的内容，都需要做add操作，然后进行commit提交操作。

##### Git的工作原理
```objc
几个核心概念
工作区（working Directory）:仓库文件夹里初.git以外的内容

版本库(repository)：.git目录，用于存储记录版本信息
暂缓区(stage)：
分支(master）：git自动创建的第一个分支
HEAD指针:用于指向当前分支

git add和 git commit的原理
git add ：把文件修改或者新添加的文件添加到暂缓区
git commit：把暂缓区的所有内容提交到当前分支
```

##### Git常见命令
```objc
git init ：初始化
git status： 查看状态
git add Dog.h   | git add Dog.h Dog.m  | git add .    // 添加到暂缓区
git commit -m "提交狗类" // 提交到版本库，注释后面什么也不跟就是提交所有，也可以指定特定的文件
git rm Dog.h ： 删除工作区中的Dog.h文件，删除操作不需要添加到暂缓区，然后直接提交就行
git log ： 查看版本信息，版本号是使用SHA加密的40位字符串
git reflog : 查看版本信息，使用这种方式查看版本信息，是可以查看到版本退回操作的， 而使用git log 看不到
git --bare init: 创建共享版本库
git push : 把本地版本库提交到远程版本库
```

##### git版本回退的两种操作
```objc
1、修改了内容，但是还没有提交到版本库
git reset --hard HEAD
2、修改了内容，并且已经提交到了版本库
git reset --hard HEAD^      			回到上一个版本
git reset --hard HEAD^^				回到上上一个版本
git reset --hard HEAD^^^			回到上上上一个版本
通过版本号回到指定的版本
git reset --hard 09873(五位数的版本号)
```

##### 创建共享版本库
```objc
创建共享版本库的几种情况：
1、搭建服务器，在服务器端设置共享版本库。有能力的正规公司这么做；
2、把共享版本库创建在某个文件夹中；
3、把共享版本库创建在U盘中
4、把共享版本库托管在第三方网站上；

这里针对第二种方式做步骤操作说明：
初始化共享版本库
git --bare init
1、项目经理要对共享版本库做初始化操作
	1.1、项目经理先连接到共享版本库，把版本库下载到本地
	git clone 路径
	1.2、项目经理完成忽略操作
		2.1.1、创建忽略文件.gitignore
		touch .gitignore
		2.1.2、设置忽略文件内容，直接去GitHub 上搜索.gitignore,找到对应的语音粘贴复制下来就行
		2.1.3、把文件添加到暂缓区
		git add .gitignore
		2.1.4、把暂缓区中的内容提交到本地版本库
		git commit -m "创建忽略文件"
		2.1.5、把本地版本库提交到共享版本库
		git push
```
