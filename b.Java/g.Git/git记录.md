## git更换token

https://blog.csdn.net/weixin_51696091/article/details/123960933

git remote -v 查看remote分支

git remote rm ${origin}  删除过期分支

git remote add origin https://新的token@github.com/账号名称/仓库名字.git   可以只写到.git前面

git push --set-upstream origin main



修改当前仓库的.git文件夹下面的config文件

如果想用一个token修改多个仓库：url配置成git@github.com:${username}/${repo}.git



本地仓库需要同步远程仓库的内容：

git fetch origin 					将main分支的内容同步到origin分支

git merge origin/main		将origin的更改合并到main中，内容更新完成
