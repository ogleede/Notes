# Git

## Git概述

- 分布式版本控制系统

- 版本控制是一种记录文件内容变化，以便将来查阅特定版本修订情况的系统。最重要的是可以记录文件修改历史记录，从而让用户能够查看历史版本，方便版本切换。

<img src="Git.assets/image-20210817125930441.png" alt="image-20210817125930441" style="zoom:50%;" />

- 工作区：存储代码的磁盘位置

- 当从暂存区提交到本地库时，就生成了历史版本，一旦生成历史版本，代码就删不掉了。

- 代码托管中心是基于网络服务器的远程代码仓库，一般称为远程库。

## Git常用命令

```git
git config --global user.name $YOURNAME  设置用户名
git config --global user.email $EMAIL		 设置用户签名
git init																 初始化本地库
git status															 查看本地库状态
git add $FILENAME												 添加到暂存区
git commit -m "日志信息" $FILENAME				提交到本地库
git reflog															 查看历史记录
git log																	 查看详细日志
git reset --hard 版本号									 版本穿梭
git checkout 														 切换分支
```

- 用户名和用户签名在/Users/ogleede/.gitconfig 用cat .gitconfig查看。第一次要设置，不然无法提交代码，作用就是区分谁提交的代码。
- ![image-20210817135526270](Git.assets/image-20210817135526270.png)

- git rm --cached hello.txt   删除暂存区里面的指定文件，工作区还存在。



## Git分支

在版本控制过程中，同时推进多个任务，可以创建每个任务的单独分支。使用分支意味着可以把自己的工作从开发主线上分离开来，开发自己分支的时候，不会影响主线分支的运行。

![image-20210817143701102](Git.assets/image-20210817143701102.png)

```git
git branch $BRANCHNAME						创建分支
git branch -v											查看分支
git branch checkout $BRANCHNAME 	切换分支
git merge $BRANCHNAME							把指定的分支合并到当前分支
```

- 合并冲突：合并分支时，两个分支在**同一个文件的同一个位置**有**两套完全不同的修改**。Git无法替我们决定使用哪一个。必须**人为决定**新代码内容。
- <img src="Git.assets/image-20210817151937248.png" alt="image-20210817151937248" style="zoom:50%;" />

下面人为修改，之后再add到暂存区->commit本地库**注意这时候不能带文件名**

手动解决冲突时，hot-fix并不会修改

master,hot-fix其实都是指向具体版本的指针，创建分支的本质就是创建一个指针，当前分支有HEAD指向。

## Git团队协作

### 团队内协作

![image-20210817152957382](Git.assets/image-20210817152957382-6912409.png)

### 跨团队协作

![image-20210817153356810](Git.assets/image-20210817153356810-6912413.png)

## GitHub

远程仓库操作

```git
git remote -v 												查看当前所有远程地址别名
git remote add $ALIAS $ADDRESS				起别名
git push $ALIAS $BRANCH								推送本地分支上的内容到远程仓库
git clone $ALIAS											将远程仓库的内容克隆到本地
git pull $ALIAS $ALIASBRANCH					将远程仓库对于分支最新内容拉下来后与当前本地分支直接合并
```

token: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

https://blog.csdn.net/weixin_41010198/article/details/119698015

之后用自己生成的`token`登录，把上面生成的`token`粘贴到`输入密码的位置`，然后成功push代码！

```git
git remote set-url $ALIAS https://<your_token>@github.com/<USERNAME>/<REPO>.git

<your_token>：换成你自己得到的token
<USERNAME>：是你自己github的用户名
<REPO>：是你的仓库名称
```

也可以 把token直接添加远程仓库链接中，这样就可以避免同一个仓库每次提交代码都要输入token了：
例如：

```GIT
git remote set-url origin https://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@github.com/shliang0603/Yolov4_DeepSocial.git/
```

克隆会做三件事：

- 拉取代码
- 初始化本地库
- 创建别名：origin

克隆push：

```git
git push https://<your_token>@github.com/<USERNAME>/<REPO>.git $BRANCH
```

SSH要设置公钥私钥



## IDEA集成Git

### IDEA Git 忽略文件

* 工程目录下.idea和.iml文件不要。只需要pom文件和代码。与项目的实际功能无关，不参与服务器上部署运行，把它们忽略掉能够屏蔽IDE工具之间的差异。

* 创建忽略规则文件 xxx.ignore，这个文件的存放位置原则上在哪都行，为了便于让~/.gitconfig文件引用，建议也放在用户家目录下

* git.ignore文件模板

    ```git
    # Compiled class file
    *.class
    
    # Log file
    *.log
    # BlueJ files
    *.ctxt
    
    # Mobile Tools for Java (J2ME)
    .mtj.tmp/
    
    # Package Files #
    *.jar
    *.war
    *.nar
    *.ear
    *.zip
    *.tar.gz
    *.rar
    
    # virtual machine crash logs, see http://www.java.com/en/download/help/error_hotspot.xml
    hs_error_pid*
    
    .classpath
    .project
    .settings
    target
    .idea
    *.iml
    ```

    * 在.gitconfig文件中引用忽略配置文件

    ```git
    [user]
    		name = ogleede
    		email = ogleede@gmail.com
    [core]
    		execludesfile = 
    		
    ```

    配置好了之后pom文件变红，表示没有添加到暂存区

* 远程库代码和本地库代码不一致，无法push。最好是先pull一下再修改本地代码，然后再push。

