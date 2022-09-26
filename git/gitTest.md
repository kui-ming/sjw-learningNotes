##### git移动文件操作

```shell
git mv gitTest.md ./dir/gitTest.md
git commit -m "备注"
git push 远程仓库名 分支名
```

通过修改文件名操作，将gitTest.md文件移动到当前目录下的dir文件夹下。PS：操作本地仓库尽量使用git命令进行操作，无需进行手动移动文件这样的操作，git命令也可以完成，并且还不需要再进行版本逻辑上的改变。

移动文件还可以使用以下操作实现：

```
git add ./dir/gitTest.md
git rm gitTest.md
```



##### 添加文件操作

```shell
git add file.txt
git commit -m "备注"
git push 远程仓库名 分支名
```

在仓库中手动添加文件后，使用

1111