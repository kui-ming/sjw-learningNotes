##### git创建本地仓库

```git
git init
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

在仓库中手动添加文件后，使用`git add`操作将file.txt添加到逻辑区，随后提交到分支中，最后推送远程仓库。



##### 删除文件操作

```shell
git rm vue/s.txt
git commit -m "备注"
git push 远程仓库名 分支名
```

使用`git rm`操作删除本地仓库中vue目录下的`s.txt`文件，随后提交到分支中，最后推送远程仓库

![image-20220926143412174](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261439341.png)