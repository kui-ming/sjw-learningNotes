# 正则表达式研究——python版

> **正则表达式**（ Regular  Expression），还可称为**规则表达式**，在代码中通常简写为**regex**、**regexp**、**re**。它是一种文本模式，使用普通字符（如a-z之间的字母）和**特殊字符**（如*+?等元字符）来组成一组规则，通过这个规则来匹配检查一个字符串中是否含有某个子串，或将匹配规则的子串替换或单独提取出来。



#### 特殊符号

| 特别字符 | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| $        | 匹配其前面的表达式出现在结尾位置，例如"d!$"匹配"hello,world!"中的"d!" |
| ^        | 后面表达式必需出现在字符串开始位置，当`^`出现在`[]`中时表示除了中括号中的字符其他都匹配 |
| *        | 匹配前面的表达式出现了零次或多次，例如”o*“可匹配"hello"和"hell" |
| +        | 匹配前面的表达式一次或多次，例如”o+“可匹配"hello"和"helloo"，但无法匹配"hell" |
| ?        | 匹配前面的表达式一次或零次，例如”o?“可匹配"hello"和"hell"，但无法匹配"helloo" |
| .        | 匹配除换行(\n)符外的任意字符，在re.S标志下.也匹配换行符      |
| ()       | 标记一个子表达式的范围。子表达式匹配的文本可被groups()获取   |
| []       | 中括号表达式，本身算一个字符，[abc]表示匹配字符a或b或c，[a-zA-Z0-9] |
|          |                                                              |



##### 特殊字符：$(匹配结尾位置)

```python
import re
pattern = re.compile(r"d!$")  # 查找d!，并且必须在结尾
print(pattern.search("hello,world!").span())    # d!出现在结尾
print(pattern.search("hello,world!aaa"))          # d!未出现在结尾
```

结果输出

```
(10, 12)
None
```



##### 特殊字符：^(匹配开头位置)

```python
import re
# ^(匹配开头位置)
pattern = re.compile(r"^he")  # 查找he，并且必须出现在开头
print(pattern.search("hello,world!").span())    # he出现在开头
print(pattern.search("hllo,world!"))    		# he不存在于开头位置，只有h是不行的
print(pattern.search("aahello,world!"))         # he未出现在开头
```

结果输出

```
(0, 2)
None
None
```



##### 特殊字符：*(匹配前面字符0次或多次，限定符)

```python
import re
# *(匹配前面字符0次或多次)
'''search方法在找到第一个匹配的子字符串后就会返回'''
pattern = re.compile(r"he*")    # 查找he,其中e可出现0次或多次
print(pattern.search("hello,world!").group())   # 匹配了he
print(pattern.search("halou,hehehe").group())    # 会匹配h，后面的he不会匹配，因为有h存在，e出现0次也会匹配
print(pattern.search("你好,世界heeeehe").group())  # 会匹配heeee，后面的he算第二个匹配子字符串，不会被返回
print(pattern.search("abcdefg"))                # 找到e，但没有与h相邻，匹配失败
```

结果输出

```
he
h
heeee
None
```



##### 特殊字符：+(匹配前面字符1次或多次，限定符)

```python
import re
# +(匹配前面字符1次或多次，限定符)
pattern = re.compile(r"he+")    # 查找he,其中e出现1次或多次
print(pattern.search("hello,world!").group())    # 匹配了he
print(pattern.search("halou"))                   # 匹配失败，找到h但没有e
print(pattern.search("你好,世界heeeehe").group())  # 会匹配heeee，后面的he算第二个匹配子字符串，不会被返回
print(pattern.search("abcdefg"))                 # 找到e，但没有与h相邻，匹配失败
```

结果输出

```
he
None
heeee
None
```



##### 特殊字符：?(匹配前面字符0次或1次，限定符)

```python
import re
# ?(匹配前面字符0次或1次，限定符)
pattern = re.compile(r"he?")    # 查找he,其中e出现0次或1次
print(pattern.search("hello,world!").group())      # 匹配到he，其中e出现1次
print(pattern.search("halou,hehe").group())        # 匹配到h，其中e出现0次
print(pattern.search("你好,世界heeeehe").group())   # 匹配到he，其中e出现1次，后面的e不再匹配
print(pattern.search("abcdefg"))                   # 没有匹配项
```

结果输出

```
he
h
he
None
```



##### 特殊字符：.(可以匹配任意字符，\n换行符除外)

```python
import re
# .(可以匹配任意字符，\n换行符除外)
pattern = re.compile(".")   # 查找任意字符
print(pattern.search("ab").span())  # 匹配a
print(pattern.search("\t").span())   # 匹配制表符
print(pattern.search("+").span())   # 匹配+号
print(pattern.search("\n"))         # 无法匹配换行符
pattern = re.compile(".", re.S)     # 指定表达式的匹配模式
print(pattern.search("\n").span())  # 在指定模式下可以匹配换行符
```

结果输出

```
(0, 1)
(0, 1)
(0, 1)
None
(0, 1)
```



##### 特殊字符：+或*和?搭配

​		`+`和`*`都是贪婪的，它们会尽可能的匹配多的字符，这在有的情况下是不需要的，而在他们后面加上`?`就可以实现非贪婪的最小匹配

```python
import re
# +或*和?搭配
'''
+和*都是贪婪的，它们会尽可能的匹配多的字符，这在有的情况下是不需要的，而在他们后面加上?就可以实现非贪婪的最小匹配
'''
pattern = re.compile("<.+>")    # 查找标签，不搭配?问号的情况下
print(pattern.search("<h1>这是标题</h1>").group())  # 匹配所有字符
pattern = re.compile("<.*?>")   # 查找标签，搭配?问号的情况下
print(pattern.search("<h1>这是标题</h1>").group())  # 匹配<h1>
```

结果输出

```
<h1>这是标题</h1>
<h1>
```

