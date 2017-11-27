## git常用命令记录

生成ssh

`ssh-keygen -t rsa -C "email@xxx.com"`

查看状态

`git status`

提交到本地

`git commit -m "msg"`

推送到远程

`git push`

拉取

`git pull`

查看日志

`git log`

回退

`git reset --hard commit-id`

推送修改

`git push -f -u origin master`



常见问题：

一：把某些目录或文件加入忽略规则，按照上述方法定义后发现并未生效，

原因是.gitignore只能忽略那些原来没有被追踪的文件，如果某些文件已经

被纳入了版本管理中，则修改.gitignore是无效的。那么解决方法就是先把

本地缓存删除（改变成未被追踪状态），然后再提交：

    `git rm -r --cached .`

git add .

git commit -m 'update .gitignore'



