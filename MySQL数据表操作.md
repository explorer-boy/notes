# 数据库
## 创建数据库
```sql
CREATE DATABASE databaseName
ON PRIMARY
(
NAME = 'databaseName_data',
FILENAME = 'D:\SQL SERVER\databaseName_data.mdf',
SIZE = 3MB,
MAXSIZE = UNLIMITED,
FILEGROWTH = 1MB
),
FILEGROUP stu_gp   /*创建另一个文件组stu_gp, 并存放文件student_data2*/
(
NAME = 'databaseName_data2',
FILENAME = 'D:\SQL SERVER\databaseName_data2.ndf',
SIZE = 3MB,
MAXSIZE = 20MB,
FILEGROWTH = 1MB
)
LOG ON
(
NAME = 'databaseName_log',
FILENAME = 'D:\SQL SERVER\databaseName_log.ldf',
SIZE = 3MB,
MAXSIZE = 20MB,
FILEGROWTH = 1MB
)
```

## 修改数据库
- 添加数据文件
```sql
ALTER DATABASE databaseName
ADD FILE
(
NAME = 'databaseName_data3',
FILENAME = 'D:\SQL SERVER\databaseName_data3.ndf',
SIZE = 3MB,
MAXSIZE = 20MB,
FILEGROWTH = 1MB
)
```
- 添加日志文件
```sql
ALTER DATABASE databaseName
ADD LOG FILE
(
NAME = 'databaseName_log2',
FILENAME  = 'D:\SQL SERVER\databaseName_log2.ldf',
SIZE = 1MB,
MAXSIZE = 10MB,
FILEGROWTH = 10%
)
```
- 删除文件
```sql
ALTER DATABASE databaseName
REMOVE FILE databaseName_data3
```
- 修改文件属性
```sql
ALTER DATABASE databaseName
MODIFY FILE
(
NAME = 'databaseName_data',
FILENAME = 'D:\databaseName_data.mdf',
SIZE = 4MB,
MAXSIZE = 10MB,
FILEGROWTH = 2MB
)
```
- 重命名数据库
```sql
ALTER DATABASE databaseName
MODIFY NAME = newDatabaseName
```

## 删除数据库
```sql
DROP DATABASE databaseName
```

---


==数据库：student_database，数据表：student_tb, student2_tb, teacher_tb==
# 表操作
---
## 创建数据表
```sql
USE student_database
CREATE TABLE student_tb
(
stunumber   char(6) NOT NULL PRIMARY KEY,
stuname     char(8) NOT NULL,
stusex      char(2) NOT NULL,
)
```

## 修改数据表
### 基本操作
- 修改表名
```sql
USE student_database
EXEC sp_rename 'student_tb', 'student'
```
- 修改列名
```sql
USE student_database
EXEC sp_rename 'student.stunumber', 'stuid', 'column'
```
- 修改列的属性
```sql
USE student_database
ALTER TABLE student
ALTER COLUMN stuname nvarchar(10)
```
- 增加列
```sql
USE student_database
ALTER TABLE student
ADD stuage int NOT NULL
```
- 删除列
```sql
USE student_database
ALTER TABLE student
DROP COLUMN stuage
```

### 约束
- 设置主键约束
```sql
USE student_database
ALTER TABLE student_tb
ADD CONSTRAINT pk_stunumber PRIMARY KEY(stunumber)
```
- 设置外键约束
```sql
USE student_database
ALTER TABLE student_tb
ADD CONSTRAINT fk_teanumber FOREIGN KEY(teanumber) REFERENCES teacher_tb(teanumber)
```
- 设置UNIQUE 约束
```sql
USE student_database
ALTER TABLE student_tb
ADD CONSTRAINT uk_stunumber UNIQUE(stunumber)
```
- 设置CHECK 约束
```sql
USE student_database
ALTER TABLE student_tb
ADD CONSTRAINT ck_stusex CHECK(stusex='男' OR stusex='女')
```
- 设置DEFAULT 约束
```sql
USE student_database
ALTER TABLE student_tb
ADD CONSTRAINT dk_stusex DEFAULT '男' FOR stusex
```
- 删除约束
```sql
USE student_database
ALTER TABLE student_tb
DROP CONSTRAINT dk_stusex /*约束名*/
```

## 删除数据表
```sql
USE student_database
DROP TABLE student
```

---

# 语句操作
## 插入语句
```SQL
USE student_database
INSERT INTO student_tb values
('202201', '张三', '男', 10, '102201'),
('202202', '李四', '男', 11, '102202'),
('202203', '二丫', '女', 12, '102203');
GO
SELECT * FROM student_tb
```

## 修改语句
```SQL
USE student_database
UPDATE student_tb SET stuname='王权' WHERE stunumber='202201'
SELECT * FROM student_tb
```

## 复制数据到指定表
- 方法一：
```SQL
/*目标表存在*/
USE student_database
INSERT INTO student2_tb/*目标表*/(stunumber, stuname, stusex, stuage,teanumber) SELECT stunumber, stuname, stusex, stuage,teanumber FROM student_tb/*原表*/
SELECT * FROM student2_tb
```
- 方法二：
```SQL
/*目标表不存在*/
USE student_database
SELECT stunumber, stuname, stusex, stuage,teanumber INTO student2_tb/*目标表*/(stunumber, stuname, stusex, stuage,teanumber) FROM student_tb/*原表*/
SELECT * FROM student2_tb
```

## 删除语句
```SQL
USE student_database
DELETE FROM student_tb WHERE stunumber='202201'
```
---

# 查询
## 投影查询
### 投影列
```SQL
USE stsc
SELECT *
FROM student
```
### `AS` 修改查询结果的列标题
```SQL
USE stsc
SELECT stno AS '学号', stname AS '姓名', tc AS '总学分'
FROM student
```
### `DISTINCT` 去掉重复行
```SQL
USE stsc
SELECT DISTINCT speciality
FROM student
```

## 选择查询
| 查询条件 | 谓词                             |
| -------- | -------------------------------- |
| 比较     | <=、<、=、>=、>、!=、<>、!>、!<  |
| 指定范围 | BETWEEN AND、NOT BETWEEN AND、IN |
| 确定集合 | IN、NOT IN                       |
| 字符匹配 | LIKE、NOT　LIKE                  |
| 空值     | IS　NULL、IS NOT NULL            |
| 多重条件 | NOT、AND、OR                     |

### `TOP` 关键字
```SQL
SELECT TOP 10 * FROM student WHERE grade /*查询成绩前10的学生*/
SELECT TOP 10 PERCENT * FROM student WHERE grade /*查询成绩前10%的学生*/

UPDATE TOP 5 student SET ssex='男' /*修改前5名学生性别为男*/
```
### `ORDER BY` 关键字（排序）
| 关键字 | 说明 |
| ------ | ---- |
| ASC    | 升序 |
| DESC   | 降序 |
```SQL
SELECT * FROM score WHERE ORDER BY grade DESC
SELECT * FROM employee WHERE age>24 ORDER BY name ASC
```
### 范围计较
```SQL
USE stsc
SELECT * FROM score
WHERE grade IN (82, 91, 95)
```
### 模式匹配
| 符号  | 说明                         |
| ----- | ---------------------------- |
| %     | 0或多个字符                  |
| _     | 1个字符                      |
| []    | 指定范围内的任意单个字符     |
| \[^\] | 不在指定范围内的任意单个字符 |
```SQL
USE stsc
SELECT *
FROM student
WHERE stname LIKE '孙%'

SELECT * FROM student WHERE stname LIKE '[^孙, 李, 何]'
```
### `IS [NOT] NULL` 空值的使用
```SQL
USE stsc
SELECT * FROM score WHERE grade IS NOT NULL
```

## 连接查询
### 等值连接
```SQL
SELECT student.*, score.*
FROM student, score
WHERE student.stno=score.stno
```
### 自然连接（去掉等值连接中的重复列）
```SQL
SELECT student.*, score.cno, score.grade
FROM student, score
WHERE student.stno=score.stno
```
### 自连接
```SQL
USE stsc
SELECT a.cno, a.stno, a.grade
FROM score a, score b
WHERE a.cno='801' AND a.grade>b.grade AND b.stno='121002' AND b.cno='801'
ORDER BY a.grade DESC
```
### 内连接
> 可省略关键字 INNER
> ON 语句的执行会在JOIN 之前 - 效率高，内存小
> WHERE 语句会先生成完整的笛卡尔积表， 再筛选结果
```SQL
USE stsc
SELECT a.stno, a.stname, b.cno, b.grade
FROM student a JOIN score b ON a.stno=b.stno
WHERE b.cno='102' AND b.grade>=85
```
### 外连接
| 类型       | 说明                       |
| ---------- | -------------------------- |
| 左外连接   | 左边关系的未用元组配上空值 |
| 右外连接   | 右边关系的未用元组配上空值 |
| 完全外连接 | 未用元组都会配上空值       |
|            |                            |
```SQL
/*左外连接*/
USE stsc
SELECT teacher.tname, course.cname
FROM teacher LEFT JOIN course ON (teacher.tno=course.tno)

/*右外连接*/
USE stsc
SELECT teacher.tname, course.cname
FROM teacher RIGHT JOIN course ON (teacher.tno=course.tno)

/*完全外连接*/
USE stsc
SELECT teacher.tname, course.cname
FROM teacher FULL JOIN course ON (teacher.tno=course.tno)
```
### 交叉连接
```SQL
USE stsc
SELECT teacher.tname, course.cname
FROM teacher CROSS JOIN course
```

## 子查询
>	子查询不含ORDER BY 字句，不能对字句进行排序，只能对最终结果进行排序
>	IN 子查询先查询字句，再查询父句；EXISTS 查询执行顺序相反
### IN 子查询
```SQL
SELECT stno, stname
FROM student
WHERE stno IN
	( SELECT stno
	  FROM SC
	  WHERE cno IN
		  ( SELECT cno
			FROM course
			WHERE cname='信息系统'
		  )
	)
```

### 比较子查询
| 查询条件 | 谓词                             |
| -------- | -------------------------------- |
| 比较     | <=、<、=、>=、>、!=、<>、!>、!<  |
| 指定范围 | ALL、SOME、ANY |
```SQL
USE stsc
SELECT stno AS '学号'
FROM SC
WHERE cno='801' AND grade>= ANY
	( SELECT grade
	  FROM SC
	  WHERE cno='205'
	)
```

### EXISTS 子查询
```SQL
USE stsc
SELECT tname, school
FROM teacher a
WHERE EXISTS
	( SELECT *
	  FROM course b
	  WHERE a.tno=b.tno
	)
```

## SELECT 查询的其他字句
### UNION
> 可以将两个及以上的查询结果合并成一个结果集（并集）
> UNION ALL保留重复项
> 
> 说明：
> 	所有查询中的列数和列的顺序必须相同
> 	数据类型必须兼容
```SQL
USE stsc
SELECT *
FROM student
WHERE tc>50
UNION
SELECT *
FROM student
WHERE stno<121051
```

### EXCEPT, INTERSECT
> EXCEPT（差集）：左查询 - 右查询
> INTERSECT （交集）：左查询 ⋂ 右查询
```SQL
USE stsc
SELECT a.stno AS '学号', a.stname AS '姓名'
FROM student a, course b, SC c
WHERE a.stno=c.stno AND b.cno=c.cno AND c.cno='801'
EXCEPT /* INTERSECT */
SELECT a.stno AS '学号', a.stname AS '姓名'
FROM student a, course b, SC c
WHERE a.stno=c.stno AND b.cno=c.cno AND c.cno='102'
```

### CTE 字句
> 临时结果集（公用表表达式）
```SQL
/*计算从1到10的阶乘*/
WITH Cfact(n, k)
AS (
		SELECT n=1, k=1
		UNION ALL
		SELECT n=n+1, k=k*(n+1)
		FROM Cfact
		WHERE n<10
   )
SELECT n, k FROM Cfact
```

### PIVOT, UNPIVOT 行列转换
```SQl
USE stsc
SELECT tno, tname, '选项', '内容'
FROM teacher
UNPIVOT
(
	'内容'
	FRON '选项' IN(title, school) unpvt
)
```
---

# 统计计算
## 聚合函数（仅用于数值类型）
| 函数名 | 说明                                     |
| ------ | ---------------------------------------- |
| AVG    | 求组中数值的平均值                       |
| COUNT  | 求组中项数                               |
| MAX    | 求最大值                                 |
| MIN    | 求最小值                                 |
| SUM    | 返回表达式中数值的总和                   |
| STDEV  | 返回给定表达式中所有数值的统计标准差     |
| STDEVP | 返回给定表达式中所有数值的填充统计标准差 |
| VAR    | 返回给定表达式中所有数值的统计方差       |
| VARP   | 返回给定表达式中所有数值的填充统计方差   |
```SQL
USE stsc
SELECT COUNT(*) AS '总人数'
FROM student
WHERE speciality='计算机'
```

## GROUP BY 子句
```SQL
USE stsc
SELECT cno AS '课程号', AVG(grade) AS '平均成绩', COUNT(*) AS '选修人数'
FROM score
GROUP BY cno
```

## HAING 字句
> WHERE, GROUP BY, HAVING 的执行顺序：
> 
> 	执行WHERE 字句，在表中选择行
> 	执行GROUP BY 字句，对选取行进行分组
> 	执行聚合函数
> 	执行HAVING 字句，筛选满足条件的分组
```SQL
USE stsc
SELECT cno AS '课程号', AVG(grade) AS '平均分数'
FROM score
WHERE cno LIKE '8%'
GROUP BY cno
HAVING COUNT(*)>4
```
---

# 视图
> 注意：
> 	1. ==视图的改变会影响基表的改变==
> 	2. 修改视图的定义就是使用 ==ALTER== 关键字重新再创建该视图
> 
### 创建视图
> 加密视图：WITH ENCRYPTION [^ 放在 AS 之前]

> SELECT 语句的限制：
> 	1. 不能使用 INTO 关键字
> 	2. 不能引用临时表和表变量
> 	3. 不能使用 ORDER BY 语句，除非有TOP关键字
> 	4. 带表达式的视图必须明确定义组成视图的各个属性列名
```SQL
USE stsc
GO
CREATE VIEW st2_view('学号', '姓名', '课程号', '成绩')
AS
SELECT student.stno, student.stname, SC.cno, SC.grade
	FROM student, SC
	WHERE student.stno=SC.stno AND student.speciality='通信'
	WITH CHECK OPTION
GO
```

### 查询视图
```SQL
USE stsc
SELECT *
FROM st2_view
```

### 可更新视图
#### 创建视图
> 条件：
> 	1. SELECT 语句中没有聚合函数
> 	2. 没有 TOP、GROUP BY、UNION 字句
> 	3. 没有 DISTICT 关键字
> 	4. 没有基表列通过计算得到的列
> 	5. FROM 字句至少包含一个基本表
```SQL
USE stsc
GO
CREATE VIEW st_view
AS
SELECT *
	FROM student
	WHERE speciality='计算机'
GO
```

#### 插入数据
> 注意：当视图依赖的==基表有多个时不能向该视图插入数据==
```SQL
USE stsc
INSERT INTO st_view VALUES('122009', '董志强', '男', '1992-11-23', '计算机', 50)
```

#### 修改数据
> 注意：当视图依赖的==基表有多个时修改一次视图只能修改一个基表的数据==
```SQL
USE stsc
UPDATE st_view SET tc=tc+2
WHERE stno='122009'
```

#### 删除数据
> 注意：当视图依赖的==基表有多个时不能删除该视图的数据==
```SQL
USE stsc
DELETE FROM st_view
WHERE stno='122009'
```

### 删除视图
```SQL
USE stsc
DROP VIEW st_view
```
---

# 索引
> 作用：
> 	1. 提高查询速度
> 	2. 保证数据记录的唯一性
> 	3. 优化查询
> 	4. 提高 ORDER BY、GROUP BY 的执行速度

## 聚集索引：
>	一个表只能有一个聚集索引
>	索引的顺序决定数据表中记录行的顺序
>	表列定义了 PRIMARY KEY 约束和 UNIQUE 约束时会自动创建索引
```SQl
USE stsc
CREATE UNIQUE CLUSTERED INDEX idx_stno_cno ON SC(stno, cno)
```

## 非聚集索引：
>	一个表可以有多个非聚集索引
>	索引的结构完全独立于数据行的结构
>	创建 UNIQUE 约束会默认建立一个非聚集索引
```SQl
USE stsc
CREATE INDEX idx_grade ON SC(grade)
```

## 查看索引
>	系统存储过程：==sp_helpindex==
```SQL
USE stsc
GO
EXEC sp_helpindex student
GO
```

## 修改索引
>	==REBUILD== 重建索引
```SQL
USE stsc
ALTER INDEX idx_grade
	ON SC
	REBUILD
		WITH(PAD_INDEX=ON, FILLFACTOR=80)
GO
```

## 删除索引
```SQL
USE stsc
DROP INDEX SC.inx_grade
```
---

# 存储过程
> 特点：
> 	1. 存储过程已在服务器上存储
> 	2. 存储过程具有安全特性
> 	3. 存储过程允许模块化程序设计
> 	4. 存储过程可以减少网络通信流量
> 	5. 存储过程可以提高运行速度

> 分类：
> 	1. 用户存储过程
> 		1. T-SQL 存储过程：保存的 T-SQL 语句的集合
> 		2. CLR 存储过程：对 Microsoft.NET Framework 公共语言运行时（CLR）方法的引用
> 	2. 系统存储过程
> 	3. 扩展存储过程

## 创建存储过程
> 重要字段：==sysobjects==
> TYPE 的值：P-存储过程，U-表，V-视图，TF-函数，L-日志
### 不带参数的存储过程
```SQl
USE stsc
GO
/*判断是否存在该存储过程，存在则删除*/
IF EXISTS(SELECT * FROM sysobjects WHERE name='stu_score' AND TYPE='p')
	DROP PROCEDURE stu_score
GO

CREATE PROCEDURE stu_score
AS
	SELECT a.stno, a.stsex, a.stname, b.cname, c.grade
		FROM student a, course b, score c
		WHERE a.stno=c.stno AND b.cno=c.cno
		ORDER BY a.stno
GO
```
### 带参数的存储过程
- 带输入参数存储过程
```SQl
USE stsc
GO
CREATE PROCEDURE stu_cou(@num int)
AS
SELECT a.stno, a.stname, a.stsex, b.cname, c.grade
FROM student a, course b, score c
WHERE a.stno=@num AND a.stno=c.stno AND b.cno=c.cno AND c.grade=(SELECT MAX(grade) FROM score WHERE stno=@num)
GO
```
- 带默认参数存储过程
```SQL
USE stsc
GO
CREATE PROCEDURE p_stu(@n char(12)='计算机')
AS 
SELECT a.stno, a.stname, b.cno, b.grade
FROM student a, score b
WHERE a.speciality=@n AND a.stno=b.stno
GO
```
- 带输出参数存储过程
```SQL
USE stsc
GO
CREATE PROCEDURE stu_op
(
	@stu_num int,
	@stu_name char(8) OUTPUT,
	@stu_avg float OUTPUT
)
AS
SELECT @stu_name=a.stname, @stu_avg=AVG(b.grade)
	FROM student a, score b
	WHERE a.stno=b.stno AND grade IS NOT NULL
	GROUP BY a.stno, a.stname
	HAVING a.stno=@stu_num
GO
```

## 存储过程的使用
```SQL
USE stsc
GO
EXECUTE stu_score   /* 或者EXEC stu_score */
GO
```

## 修改存储过程
```SQL
USE stsc
ALTER PROCEDURE stu_score
AS
	SELECT a.stno, a.stsex, a.stname, b.cname, c.grade
		FROM student a, course b, score c
		WHERE a.stno=c.stno AND b.cno=c.cno
		ORDER BY a.stno
GO
```

## 删除存储过程
```SQL
USE stsc
DROP PROCEDURE stu_score
```
---

# 触发器
> 满足条件时，自动触发

> 分类：
> 	1. DML 触发器：
> 		事件：表或视图中修改数据的 INSERT 语句、UPDATE 语句、DELETE 语句
> 	2. DDL 触发器：
> 		事件：服务器或数据库中数据定义的 CREATE、ALTER、DROP 语句等

## 创建 DML 触发器
```SQL
USE stsc
GO
CREATE TRIGGER trig_stu
	ON student
AFTER INSERT, DELETE, UPDATE
AS
BEGIN
	SET NOCOUNT ON
	SELECT * FROM student
END
```
