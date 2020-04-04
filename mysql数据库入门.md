# mysql数据库入门

## 1、操作数据库

流程：操作数据库>操作数据库中的表>操作数据库表中的数据

### 1.1、操作数据库

1、创建数据库

```sql
CREATE DATABASE [IF NOT EXISTS] <数据库名>;
```

2、删除数据库

```sql
DROP DATABASE [IF NOT EXISTS] <数据库名>;
```

3、使用数据库

```sql
USE <数据库名>;
```

4、查看数据库

```sql
SHOW DATABASES;
```

### 1.2、数据库的列类型

> 数值

| 类型      | 长度     | 说明                             |
| --------- | -------- | -------------------------------- |
| tinyint   | 1个字节  | 十分小的数据                     |
| smallint  | 2个字节  | 较小的数据                       |
| mediumint | 3个字节  | 中等小的数据                     |
| int       | 4个字节  | 标准整数                         |
| bigint    | 8个字节  | 较大的数据                       |
| float     | 4个字节  | 浮点数                           |
| double    | 8个字节  | 双精度浮点数                     |
| decimal   | 16个字节 | 字符串形式的浮点数不存在精度损失 |

> 字符串

| 类型     | 长度           | 说明               |
| -------- | -------------- | ------------------ |
| char     | 0~255个字节    | 较短字符串         |
| varchar  | 0~65535个字节  | 常用字符串         |
| tinytext | 0~2^8-1个字节  | 微型文本           |
| text     | 0~2^16-1个字节 | 保存文本串、大文本 |

> 时间

| 类型      | 格式                | 说明                       |
| --------- | ------------------- | -------------------------- |
| date      | YYYY-MM-DD          | 日期格式                   |
| time      | HH:mm:ss            | 时间格式                   |
| datetime  | YYYY-MM-DD HH:mm:ss | 常用的日期格式             |
| timestamp | 时间戳              | 1970.1.1到当前时间的毫秒数 |
| year      |                     | 表示年份                   |

> null

- 表示没有值或未知
- == 注意：尽量不要在数据库中使用 ==

### 1.3、数据库中的字段属性

| 属性             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| unsigned         | 无符号的整数，表示该字段不能为负数                           |
| zerofill         | 0填充，不足的位数用0填充。例如：int(3) 5 - - 005             |
| auto_increment   | 自增，在上一条记录基础上+1。通常用来做主键，必须为整数类型，可以自定义初始值和步长 |
| not null         | 表明此字段不能为空值，否则会报错                             |
| null             | 表明此字段可以为空，不赋值时默认为null                       |
| default <默认值> | 通过此关键字自定义默认值，不手动赋值时用默认值填充           |
| comment<注释>    | 给字段一个说明                                               |

### 1.4、创建数据库表

示例：

```sql
CREATE TABLE IF NOT EXISTS `student` (
		`id` INT ( 4 ) NOT NULL auto_increment COMMENT '学号',
		`name` VARCHAR ( 30 ) NOT NULL DEFAULT '匿名' COMMENT '姓名',
		`pwd` VARCHAR ( 20 ) NOT NULL DEFAULT '123456' COMMENT '密码',
		`gender` VARCHAR ( 2 ) NOT NULL DEFAULT '男' COMMENT '性别',
		`birthday` datetime NOT NULL COMMENT '出生日期',
		`address` VARCHAR ( 20 ) DEFAULT NULL COMMENT '家庭住址',
		`email` VARCHAR ( 20 ) DEFAULT NULL COMMENT '邮箱',
    	PRIMARY KEY(`id`)
	) ENGINE = INNODB DEFAULT CHARSET = utf8;
-- 表名和字段名尽量用``(倒引号)包起来，可以避免与数据库关键字冲突
-- 字符串要用''(单引号)包起来
-- primary key 数据库的主键，作为数据库每一条数据的唯一标识符
-- 字段名单词间用_(下划线)做分隔符(由于sql语句不区分大小写，加分隔符增加可读性)
```

格式：

```sql
CREATE TABLE [IF NOT EXISTS] <表名>(
	<字段名> <数据类型> [属性...][索引][注释],
    ....... 
)[表类型][字符集设置][注释];
```

### 1.5、数据表类型

|            | MYISAM | INNODB            |
| ---------- | ------ | ----------------- |
| 事务支持   | 不支持 | 支持              |
| 数据行锁定 | 不支持 | 支持              |
| 外键约束   | 不支持 | 支持              |
| 全文索引   | 支持   | 不支持            |
| 表空间大小 | 较小   | 较大，约为前者2倍 |

常规使用操作：

MYISAM：节约空间，速度较快

INNODB：安全性高，可以处理事务，多表多用户操作

### 1.6、修改删除表

> 修改

```sql
-- 修改表名 : ALTER TABLE <旧表名> RENAME AS <新表名>;
ALTER TABLE `student` RENAME AS `teacher`;
-- 增加字段 : ALTER TABLE <表名> ADD <字段名> [属性];
ALTER TABLE `teacher` ADD `age` INT(10);
-- 修改表的字段
-- 修改约束 : ALTER TABLE <表名> MODIFY <字段名> <属性>;
ALTER TABLE `teacher` MODIFY `age` VARCHAR(11);
-- 修改字段名: ALTER TABLE <表名> CHANGE <旧字段名> <新字段名> [属性];
ALTER TABLE `teacher` CHANGE `age` `new_age` INT(11);
-- 删除表的字段:ALTER TABLE <表名> DROP <字段名>;
ALTER TABLE `teacher` DROP `new_age`;
```

> 删除

```sql
-- 删除表:DROP TABLE [IF EXISTS] <表名>;
DROP TABLE IF EXISTS `teacher`;
```

## 2、mysql数据管理

### 2.1、外键

> 方式一、在创建表是增加约束

```sql
CREATE TABLE `grade` ( 
    `gradeid` INT ( 10 ) NOT NULL auto_increment COMMENT '年级id', 
    `gradename` VARCHAR ( 50 ) NOT NULL COMMENT '年级名称', 
    PRIMARY KEY ( `gradeid` ) 
) ENGINE = INNODB CHARSET = utf8;


CREATE TABLE IF NOT EXISTS `student` (
		`id` INT ( 4 ) NOT NULL auto_increment COMMENT '学号',
		`name` VARCHAR ( 30 ) NOT NULL DEFAULT '匿名' COMMENT '姓名',
		`pwd` VARCHAR ( 20 ) NOT NULL DEFAULT '123456' COMMENT '密码',
		`gender` VARCHAR ( 2 ) NOT NULL DEFAULT '男' COMMENT '性别',
    	`gradeid` INT(10) NOT NULL COMMENT '年级',
		`birthday` datetime NOT NULL COMMENT '出生日期',
		`address` VARCHAR ( 20 ) DEFAULT NULL COMMENT '家庭住址',
		`email` VARCHAR ( 20 ) DEFAULT NULL COMMENT '邮箱',
    	PRIMARY KEY(`id`),
    	KEY `FK_gradeid`(`gradeid`),
    	CONSTRAINT `FK_gradeid` FOREIGN KEY(`gradeid`) REFERENCES `grade`(`gradeid`)
	) ENGINE = INNODB DEFAULT CHARSET = utf8;
```

> 方式二、创建表成功后，添加外键约束

```sql
CREATE TABLE `grade` ( 
    `gradeid` INT ( 10 ) NOT NULL auto_increment COMMENT '年级id', 
    `gradename` VARCHAR ( 50 ) NOT NULL COMMENT '年级名称', 
    PRIMARY KEY ( `gradeid` ) 
) ENGINE = INNODB CHARSET = utf8;
CREATE TABLE IF NOT EXISTS `student` (
		`id` INT ( 4 ) NOT NULL auto_increment COMMENT '学号',
		`name` VARCHAR ( 30 ) NOT NULL DEFAULT '匿名' COMMENT '姓名',
		`pwd` VARCHAR ( 20 ) NOT NULL DEFAULT '123456' COMMENT '密码',
		`gender` VARCHAR ( 2 ) NOT NULL DEFAULT '男' COMMENT '性别',
    	`gradeid` INT(10) NOT NULL COMMENT '年级',
		`birthday` datetime NOT NULL COMMENT '出生日期',
		`address` VARCHAR ( 20 ) DEFAULT NULL COMMENT '家庭住址',
		`email` VARCHAR ( 20 ) DEFAULT NULL COMMENT '邮箱',
    	PRIMARY KEY(`id`)
	) ENGINE = INNODB DEFAULT CHARSET = utf8;
-- 创建外键约束：ALTER TABLE <表明> ADD CONSTRAIN <约束名> FOREIGN KEY(<作为外键的字段>) REFERENCES <被引用的表>(<被引用的字段>);
ALTER TABLE `student` ADD CONSTRAIN `FK_gradeid` FOREIGN KEY(`gradeid`) REFERENCES `grade`(`gradeid`);
```



以上操作都是物理外键，数据库级别的外键，不建议使用。

想要使用多张表的数据，使用外键，通过程序去实现（逻辑外键）。



### 2.2、DML语言(Data Manipulation Language数据操纵语言)

> 添加：insert

```sql
INSERT INTO <表名>([字段1],[字段2]....) VALUES([值1],[值2]...),([值1],[值2]...)...;
```

注意事项：

- 可以同时插入多条数据，数据之间用,(半角逗号)隔开
- 自增字段可省略，但要保证字段与值一一对应

>修改：update

```sql
UPDATE <表名> SET <字段1>=<值1>[,<字段2>=<值2>,...] [WHERE <条件>];
```

> 删除：delete

```sql
DELETE FROM <表名> [WHERE <条件>];
```

> 清空：truncate

```sql
TRUNCATE TABLE <表名>;
```

delete和truncate的区别：

- 相同点：都能删除数据，但不会删除表结构
- 不同：
  - truncate会重置自增列计数器
  - truncate不影响事务

### 2.3、DQL语言(Data Query Language数据查询语言)

> 查询：select

```sql
SELECT * FROM <表名>; --查询全部数据
SELECT [字段1],[字段2]... FROM <表名>; --查询部分数据
SELECT [字段1] [AS <别名1>], [字段2] [AS <别名2>] ... FROM <表名>; -- 给查询出的字段起别名
SELECT DISTINCT <字段> FROM <表名>; -- 对查询结果去重

-- SELECT 完整语法
SELECT [ALL | DISTINCT] < * | TABLE.* | 
[
    [TABLE.<字段1>
     [AS <别名1>]
    ]
    [,TABLE.<字段2>
     [AS <别名2>]
    ]...
]> FROM <表名> [AS 表别名][LEFT|RIGHT|INNER JOIN <表名2>]
[WHERE <条件子句>]
[GROUP BY <字段1>[,字段2]...]
[HAVING]
[ORDER BY <字段>]
```

### 2.4、where条件子句

> 逻辑运算符

| 运算符  | 语法           | 说明   |
| ------- | -------------- | ------ |
| and &&  | A and B  A&&B  | 逻辑与 |
| or \|\| | A or B  A\|\|B | 逻辑或 |
| not !   | not A    !A    | 逻辑非 |

> 模糊查询：比较运算符

| 运算符        | 语法               | 描述                          |
| ------------- | ------------------ | ----------------------------- |
| is null       | A is null          | 若结果为null，则返回true      |
| is not null   | A is not null      | 若结果不为null，则返回true    |
| between...and | A between B and C  | 若A在B和C之间则返回true       |
| like          | A like B           | 若A匹配到B，则返回true        |
| in            | A in (b1,b2,b3...) | 若A能匹配到某一个值则返回true |

与like搭配使用的运算符：

```
_：缺省一个字符，例如：刘_ 表示全长2个字符且以刘开头的字符串
%：缺省任意字符，例如：%嘉% 表示中间含有嘉的字符串
```

### 2.5、联表查询

| 连接       | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| inner join | 在表中存在至少一个匹配时，INNER JOIN 关键字返回行。          |
| left join  | 从左表 (table_name1) 那里返回所有的行，即使在右表 (table_name2) 中没有匹配的行。 |
| right join | 从右表 (table_name2) 那里返回所有的行，即使在左表 (table_name1) 中没有匹配的行。 |



### 2.6、分页和排序



