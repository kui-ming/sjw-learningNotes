# python-docx



##### 下载

```ini
pip install python-docx
```



##### 创建文档对象Document

```python
from docx import Document

doc = Document(r'E:\Desktop\appmanage\数据库.docx')	# 为文件数据库.docx创建文档对象
```



##### 操作表格 Document.tables

```python
from docx import Document

doc = Document(r'E:\Desktop\appmanage\数据库.docx')
# for p in doc.paragraphs:
#     print(p.text+'-s')

for t in doc.tables:
    print(t)	# 输出表格对象
    for rows in t.rows:			# 循环当前表格的行
        for cell in rows.cells:	# 获取该行的所有单元格
            print(cell.text+'\t',end='')	# 输出该单元格的内容
        print()		# 换行
```

示例：

![image-20220410114005151](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220410114005151.png)



##### 操作文档段落 Document.paragraphs

```python
from docx import Document

doc = Document(r'E:\Desktop\appmanage\数据库.docx')
for p in doc.paragraphs:
    print(p.text)   # 输出此段落文本
```

示例：

![image-20220410114656555](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220410114656555.png)



##### 创建word表格

```python
from docx import Document


f = open(r'test.docx', 'w', encoding='utf-8')   # 创建一个word文档
f.close()   # 关闭
doc = Document()    # 新建一个空白word模板
drive = mysql.get_mysql()   # 从数据库拿数据
drive.use_database('test')  # 切换到名为test的数据库
ts = drive.show_table_name().get('data', [])    # 获取所有test数据库中所有表名
for index, t in enumerate(ts):
    struct = drive.show_table_structure(t['table_name'])['data']    # 获取表结构
    print(struct)	# 输出表结构
    table = doc.add_table(rows=1, cols=5, style='Table Grid')   # 添加一个一行五列的表格，样式为网格表格
    row = table.rows[0] # 获取到表格第一行
    row.cells[0].text = '列名'    # 获取一行中的第一个单元格，设置文本
    row.cells[1].text = '类型'
    row.cells[2].text = '是否为空'
    row.cells[3].text = '主键'
    row.cells[4].text = '备注'
    for i,s in enumerate(struct):   # 循环表结构数据，添加行
        table.add_row()             # 添加新行
        row = table.rows[i+1]       # 获取当前行，第一行是默认固定的，忽略
        row.cells[0].text = s.get('字段')     # 设置此单元格文本
        row.cells[1].text = s.get('数据类型')
        row.cells[2].text = s.get('是否为空')
        row.cells[3].text = s.get('主键')
        row.cells[4].text = s.get('说明')
        doc.add_paragraph() # 添加段落，为的是让多个表格之间有空格
doc.save('test.docx')		# 将此文档保存到指定文件
```

示例：

![image-20220505092827905](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220505092827905.png)

![image-20220505092836166](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220505092836166.png)



##### 修改文档默认样式

```python
from docx import Document
from docx.oxml.ns import qn

doc = Document()    # 新建一个空白word模板
doc.styles['Normal'].font.name = u'宋体'
doc.styles['Normal']._element.rPr.rFonts.set(qn('w:eastAsia'),u'宋体')

```

示例：

![image-20220505104710218](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220505104710218.png)



##### 修改表格样式、居中

```python
from docx import Document
from docx.enum.table import WD_ALIGN_VERTICAL	# 此处会报错，但不影响使用
from docx.oxml.ns import qn

doc = Document('test.docx')
for table in doc.tables:    # 访问文档中所有表格
    for row in table.rows:  # 访问当前表格中所有行
        for cell in row.cells:  # 访问当前行中所有单元格
            cell.vertical_alignment = WD_ALIGN_VERTICAL.CENTER  # 设置当前单元格样式为垂直居中对齐
            for para in cell.paragraphs:    # 访问当前单元格中的所有文本段落
                para.alignment = WD_ALIGN_VERTICAL.CENTER   # 设置当前文本段落居中对齐
                # 段落文本中不同样式的文本属于一个run，因为当前段落只有一个文本样式，所以只有访问第一个run即可
                para.runs[0].font.name = 'Arial'    # 设置字体样式，如果不设置的话无法修改为宋体
                para.runs[0]._element.rPr.rFonts.set(qn('w:eastAsia'),'宋体')
doc.save('test.docx')   # 保存到文件
```

示例：

![image-20220505105934831](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220505105934831.png)



##### 设置段落前后间距

```python
import docx.shared as shared

def set_space(paragraph, b_pt=-2, a_pt=-2):
    """
    设置段落前后间距,如果只设置b_pt值则同时设置前后间距,如果一个值为-1则证明对应间距不会改变
    :param paragraph:段落对象
    :param b_pt: int,固定值，磅，前间距值
    :param a_pt: int,固定值，磅，后间距值
    :return: 返回自身
    """
    try:
        if b_pt >= 0 and a_pt == -1:
            paragraph.paragraph_format.space_before = shared.Pt(b_pt)  # 段前间距
        elif a_pt >= 0 and b_pt == -1:
            paragraph.paragraph_format.space_after = shared.Pt(b_pt)  # 段后间距
        elif b_pt >= 0 > a_pt:
            paragraph.paragraph_format.space_before = shared.Pt(b_pt)  # 段前间距
            paragraph.paragraph_format.space_after = shared.Pt(b_pt)  # 段后间距
        elif b_pt >= 0 and a_pt >= 0:
            paragraph.paragraph_format.space_before = shared.Pt(b_pt)  # 段前间距
            paragraph.paragraph_format.space_after = shared.Pt(a_pt)  # 段后间距
    except Exception:
        traceback.print_exc()
    return paragraph
```



##### 设置段落对齐方式

```python
from docx.enum.text import WD_ALIGN_PARAGRAPH

def set_paragraph_alignment(paragraph, alignment="center"):
    """
    设置段落对齐方式，不设置alignment时默认为居中
    :param paragraph: 段落对象
    :param alignment: center或1为居中，left或0为左对齐，right或2为右对齐，justify或3为两端对齐
    :return: 段落本身
    """
    try:
        alignment = alignment.lower()
    except AttributeError as e:
        pass
    v_alignment = WD_ALIGN_PARAGRAPH.CENTER       # 居中对齐，常量1
    if alignment == 'left' or alignment == 0:
        v_alignment = WD_ALIGN_PARAGRAPH.LEFT     # 左对齐，常量0
    elif alignment == 'right' or alignment == 2:
        v_alignment = WD_ALIGN_PARAGRAPH.RIGHT    # 右对齐，常量2
    elif alignment == 'justify' or alignment == 3:
        v_alignment = WD_ALIGN_PARAGRAPH.JUSTIFY  # 两端对齐，常量3
    paragraph.paragraph_format.alignment = v_alignment
    return paragraph
```



##### 设置段落缩进

```python
import docx.shared as shared

def set_paragraph_indent(paragraph, char_num=2, indent='frist'):
    """
    设置段落缩进
    :param paragraph: 段落对象
    :param char_num:int,缩进字符数
    :param indent: frist或0为首行缩进，left或1为左缩进，right或2为右缩进
    :return: 段落本身
    """
    try:
        indent = indent.lower()
    except Exception:
        pass
    size = paragraph.style.font.size
    if not size:    # 如果没有设置大小
        size = shared.Inches(0.5)   # 相当于4空格
    if indent == 'frist' or indent == 0:
        # 首行缩进char_num个字符，获取段落字体大小，然后缩进n个字符大小的距离就是字符数
        print(paragraph.style.font.size)
        paragraph.paragraph_format.first_line_indent = size * char_num
    elif indent == 'left' or indent == 1:
        paragraph.paragraph_format.left_indent = size * char_num
    elif indent == 'right' or indent == 2:
        paragraph.paragraph_format.right_indent = size * char_num
    return paragraph
```

