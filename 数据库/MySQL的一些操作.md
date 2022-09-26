# MySQL的一些操作



查询某数据库中的所有数据表名

```mysql
SELECT table_Name FROM information_schema.TABLES where table_schema = 'test'   # test为数据库名
```



查询某个表的表结构

```mysql
select 
	COLUMN_NAME as 字段,
	COLUMN_TYPE as 数据类型,
	DATA_TYPE as 类型,
	CHARACTER_MAXIMUM_LENGTH as 长度,
	COLUMN_KEY as 主键,
	IS_NULLABLE as 是否为空,
	COLUMN_DEFAULT as 默认值,
	COLUMN_COMMENT as 说明,
	extra as 额外
from information_schema.columns
where 
	table_schema = 'test' # 数据库名
	and 
	table_name = '表0' # 数据表名	

```

