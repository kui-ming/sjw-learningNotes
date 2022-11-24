##### git创建本地仓库

```git
git init
```



##### 查看当前仓库状态

```shell
git status
```



##### 修改分支名

```shell
git branch -m 旧分支名 新分支名 # 将指定分支名修改为新的分支名

git branch -M 旧分支名 新分支名 # 如果新分支名已存在，则需要使用-M强制修改

git branch -m 新分支名 # 修改当前分支的名称

git branch -M 新分支名 # 强制修改当前分支的名称
```



##### 查看远程仓库

```shell
git remote -v
```



##### 添加远程仓库

```shell
git remote add 名称 远仓地址	# 需要设置远程仓库的名称和指定远仓的地址
```



##### 删除远程仓库

```shell
git remote rm 远仓名称
```



##### 修改远程仓库

```shell
git remote set-url --push 远仓名称 新的远程地址 # 通过远仓名称来修改远仓的地址
```



##### 推送远程仓库

```shell
git push 远仓名称 [本地分支] # 如果不指定本地分支则推送当前分支
```



##### 拉取远程仓库

```shell
git pull 远仓名称 [本地分支] # 如果不指定本地分支则拉取到当前分支
```



##### git移动文件操作

```shell
git mv gitTest.md dir
git commit -m "备注"
git push 远程仓库名 分支名
```

通过修改文件名操作，将gitTest.md文件移动到当前目录下的dir文件夹下。PS：操作本地仓库尽量使用git命令进行操作，无需进行手动移动文件这样的操作，git命令也可以完成，并且还不需要再进行版本逻辑上的改变。

移动文件还可以使用以下操作实现：

```
git mv ./dir/gitTest.md
```



##### 添加文件操作

```shell
git add file.txt
git commit -m "备注"
git push 远程仓库名 分支名
```

在仓库中手动添加文件后，使用`git add`操作将file.txt添加到逻辑区，随后使用`git commit`提交到分支中，最后`git push`推送远程仓库。

##### 添加全部更新的文件

```shell
git add -A 
```

`git add -A`可以把所有修改后的或新增的文件添加到git逻辑区进行管理，等待合并到分支中去。



##### 删除文件操作

```shell
git rm vue/s.txt # 此删除模式会将此文件从工作区删除
git commit -m "备注"
git push 远程仓库名 分支名
```

使用`git rm`操作删除本地仓库中vue目录下的`s.txt`文件，随后提交到分支中，最后推送远程仓库

![image-20220926143412174](../../MDimages/gitTest_images/images202209261439341.png)



##### 取消git对文件夹的管理

```shell
git rm -r --cached 文件名/目录名
```

`-r` 会递归删除指定目录中的包括子目录下的所有文件，`--cached` 规定只在逻辑区中删除该文件，也就是git不再管理该文件，但工作区中的实体还在



#### git忽略指定文件

在Git管理的项目的根路径下新建`.gitignore`文件，这是git规定忽略文件，在这个文件中添加不想git管理的文件或文件夹

