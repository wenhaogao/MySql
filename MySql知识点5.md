-- 视图(虚拟表 创建表结构 数据存储在DBMS)

-- 物理表(创建表结构 数据存储在数据文件)

-- 存储引擎(MYSQL 5.x INNODB) 

-- 创建一张表的时候默认创建两个文件(frm 存储当前表的结构 ibd存储数据和索引)

-- 虚拟表 在数据文件中只会保存当前数据表的结构

-- 视图 数据来自于某一张或多张物理表的数据(视图是对一张或多张物理表的映射)

-- 视图中真实是不存在的数据

-- 视图作用(数据的隐藏)

-- 学员表(id name address score) 系统角色   教师  针对于不同角色来创建不同的视图

-- 权限 视图达到对数据颗粒度更小的控制 

-- 视图 是数据对象(视图 数据表 数据库 约束)  数据对象 使用create drop

SHOW TABLES

-- 创建视图

-- create 创建  类型view  视图名

```
CREATE VIEW student_view
```


-- 区分定义和视图的数据内容分割
AS
 
```
SELECT * FROM student
```

  
  
 -- 视图 指向了一个select 查询视图的 其实查询的视图内部的select


```
SELECT * FROM student_view


DROP VIEW student_view

CREATE VIEW student_view
AS
 SELECT stuId,stuName FROM student WHERE stuId<20
 
 
SELECT * FROM student_view
```

-- 视图增加数据
 
 
```
INSERT INTO student_view VALUES(105,'张三丰')
 
 SELECT * FROM student_view
 
 SELECT * FROM student
```

 -- 视图修改数据
 
 
```
UPDATE student_view  SET stuName='MYSQL' WHERE stuId=11
 
 SELECT * FROM student_view
 
 SELECT * FROM student
```

 -- 视图删除数据
 
 
```
DELETE FROM student_view WHERE stuId=11
```

 
 -- 在视图上的操作会影响物理表的数据
 
 -- 内链接

```
CREATE VIEW student_class_view
 AS
 SELECT S.stuId,S.stuName,S.stuSex,S.stuAge,C.className FROM student S INNER JOIN class C
  ON S.classId=C.classId 
 
 
 
 SELECT * FROM student_class_view
```

 -- 不能添加关联多张表的视图(增加)
 
```
INSERT INTO student_class_view VALUES(106,'殷素素','女',44,'10001')
```

 -- 修改不改动表的结构情况下是可以修改的

```
UPDATE student_class_view SET stuName='殷素素' WHERE stuId=13
```


-- 删除也不行删除关联多张表的数据

```
DELETE FROM student_class_view WHERE stuId=12 
 
 SELECT * FROM student_class_view
 
 SELECT * FROM student
```

 -- 视图 查询语句(MYSQL 支持的select都能创建视图)

```
DROP VIEW student_class_view
```





-- 索引 

-- 数据库 主要功能存储数据  查询到数据  查询的性能比较重要

-- 查询的性能 主要体现在 索引 

-- MYSQL 索引 按照索引值的算法  1:b-tree  2:hash 索引

-- b-tree 是在MYSQL中索引比较多的索引(INNODB MYISAM 默认支持b-tree 索引)

-- hash MYSQL中只有memory 存储引擎支持(其他的存储引擎默认不支持)

-- 主键(主键既是一种约束也是一种索引 默认的索引)

```
SELECT * FROM student

SELECT S.stuId,S.stuName,S.stuSex,S.stuAge,C.className FROM student S INNER JOIN class C
ON S.classId=C.classId
```

-- 索引 当在一张表中取部分数据的时候才会使用索引(行定位 或者行过滤的时候)

-- select * from student  不会使用索引 
 
 
```
EXPLAIN SELECT * FROM student
 
 EXPLAIN SELECT * FROM student WHERE stuId=10
```

 
 -- 主键(默认使用主键索引)
 
 -- 过滤的条件 stuName  stuName列上没有索引  所以当前的语句不适用索引 
 
```
EXPLAIN SELECT * FROM student WHERE stuName='赵敏'
```

 -- 使用or 也不适用索引
 
```
EXPLAIN SELECT * FROM student WHERE stuId=10 OR stuName='赵敏'
```

 -- and 使用索引
 
```
EXPLAIN SELECT * FROM student WHERE stuId=10 AND stuName='赵敏'
```

 -- 所以列参与运算 也不适用索引
 
```
EXPLAIN SELECT * FROM student WHERE NOT stuId+1=10
 EXPLAIN SELECT * FROM student WHERE stuId+1>10
```


 -- 创建一张表 数据文件两种 frm 表结构  ibd 存储数据和索引  
 
 -- 索引是要存储在数据文件中 索引占数据空间 
 
 -- 数据的修改删除增加 都会影响索引
 
 
 -- 索引的选取(1:数据的改动一定要最少
 
 2:索引列的数据量要尽量的小
 
 3:索引列数据不能重复)
 
 -- 单例索引(数据对象)
 
```
CREATE INDEX student_index ON student(stuName)
 
 EXPLAIN SELECT * FROM student WHERE stuName='赵敏'
 
 EXPLAIN SELECT * FROM student WHERE stuId=10 AND stuName='赵敏'
 
 EXPLAIN SELECT * FROM student WHERE stuId=10 OR stuName='赵敏'
 
 EXPLAIN SELECT * FROM student WHERE stuName LIKE '赵%'
 
 EXPLAIN SELECT * FROM student WHERE stuName LIKE '%赵%'
 
 EXPLAIN SELECT * FROM student WHERE stuName LIKE '_赵%'

 EXPLAIN SELECT * FROM student WHERE stuAge=32 OR stuName = '赵敏'
 
 EXPLAIN SELECT * FROM student WHERE stuAge=32 AND stuName = '赵敏'
 
 -- 删除指定的索引
 DROP INDEX student_index ON student
 
 -- 组合索引(联合主键)
 CREATE INDEX student_index ON student(stuSex,stuName)
 
 EXPLAIN SELECT * FROM student WHERE stuName='赵敏'
 
 EXPLAIN SELECT * FROM student WHERE stuSex='男'
 
  EXPLAIN SELECT * FROM student WHERE stuName='赵敏' AND stuSex='男'
  
  EXPLAIN SELECT * FROM student WHERE stuSex='男' AND stuName='赵敏'
  
  EXPLAIN SELECT * FROM student WHERE stuName='赵敏' OR stuSex='男'

  DELETE FROM student WHERE stuId=105
  
  DROP INDEX student_index ON student
```

  -- 唯一索引(唯一约束 唯一约束就是唯一索引)
 
```
CREATE UNIQUE INDEX student_stuName ON student(stuName) 
  
  
  SELECT * FROM student
  
  EXPLAIN SELECT * FROM student WHERE stuName='赵敏'
```

  
  -- 全文索引(MYISAM 中使用)
  
```
CREATE FULLTEXT INDEX student_index ON student(stuName)
```
