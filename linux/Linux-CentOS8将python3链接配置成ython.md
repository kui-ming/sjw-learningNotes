# Linux—CentOS8系统将python3链接配置成Python

##### 缘由

​		新买了台服务器，默认安装的CentOS8的系统，发现使用python指令显示无效，接着在/usr/bin里面找python的软链接，发现只有python3，于是使用命令

```sh
 ln -s ./python3 ./python # 我这已经到达了/usr/bin目录下
```

![image-20221026205223153](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202210262055907.png)

​		另外，我想直接链接到源文件，那就用`ls -l`看看它的原地址在哪吧

```sh
ls -l /usr/bin/python*
rm -f /usr/bin/python
ln -s /etc/alternatives/python3 /usr/bin/python
```

![image-20221026211548373](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202210262125028.png)

​		链接好python后，pip也想改一下

```sh
ls -l /usr/bin/pip*
ln -s /etc/alternatives/pip-3 /usr/bin/pip
```

![image-20221026212519626](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202210262125798.png)

> 软链接要在`ln`命令后加`-s`（symbolic），软链接与硬链接不同的是软链接只生成一个镜像，但硬链接会拷贝源文件

##### 结果

​		创建完软链接后再次查看/usr/bin下是否存在python，结果自然是存在，然后运行也是可以的

![image-20221026205452389](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202210262055908.png)



