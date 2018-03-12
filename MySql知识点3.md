-- 创建表 修改表 约束
-- 删除 更新 增加

```
SHOW TABLES

CREATE TABLE student(
    stuId INT AUTO_INCREMENT,
    stuName VARCHAR(20),
    stuSex ENUM('男','女'),
    stuAge INT,
    PRIMARY KEY(stuId)
)
```


-- 增加
-- 一次增加一条数据(values(值列表) 值的顺序必须按照创建表的顺序)

```
INSERT INTO student VALUES(DEFAULT,'张三','男',22)
```


-- 多条

```
INSERT INTO student VALUES(DEFAULT,'李四','男',22),
(DEFAULT,'王五','男',22),
(DEFAULT,'张无极','男',22),
(DEFAULT,'张三风','男',22)

SELECT * FROM student
```


-- 定义添加数据的顺序

```
INSERT INTO student(stuName,stuAge,stuSex,stuId)
 VALUES('张翠山',44,'男',DEFAULT)
```

-- 增加部分数据


```
INSERT INTO student(stuId,stuName)
  VALUES(DEFAULT,'赵敏')
```

-- 复制表并复制表中数据

```
SHOW TABLES
```

-- 创建表student1 根据后面的查询语句(结果集来创建当前的表)

-- student1 表中的结构完全是按照后面的查询语句的结果集(结果返回的是什么

-- student1 表的结构就是什么样子)

-- 并把后面的查询语句的查询的数据复制到创建的student1表中

-- 注意:约束和自动增长不会复制  创建的表不能提前存在

```
CREATE TABLE student1 AS SELECT * FROM student
```

-- student2 的结构就只有stuId,stuName

```
CREATE TABLE student2 AS SELECT stuId,stuName FROM student
```



```
CREATE TABLE student3 AS SELECT * FROM student WHERE 1=2  -- 条件(关系表达式  1=2)
```


-- 只会复制表的数据不会创建表(要求增加数据的表(student3)提前存在 不存在报错)

```
INSERT INTO student4 SELECT * FROM student
```




```
DROP TABLE student3,student1

SHOW TABLES
```


-- 删除

-- 删除指定表的所有的数据


```
DELETE FROM student
```

-- 删除指定表的满足where条件的数据

```
DELETE FROM student WHERE stuId=1
```


-- 截断表(删除指定表的所有的数据 不能删除部分数据 delete from student)

```
TRUNCATE TABLE student
```


-- 截断表和delete的区别

-- 1:数据越大性能差距越大(截断表性能大大的优于delete)

-- 2:截断表删除的数据不能恢复

delete删除的数据可以恢复

-- 3:截断表删除的数据会使自增长的值初始 delete不会初始自增长的值


```
CALL proc(1000)

SELECT COUNT(stuId) FROM student

DELETE FROM student

TRUNCATE TABLE student

INSERT INTO student VALUES(DEFAULT,'张无忌','女',22)

SELECT * FROM student
```

-- 更新

```
UPDATE student SET stuName='',stuSex='',stuAge=33 WHERE stuId=1

UPDATE student SET stuName='' WHERE stuId=1

UPDATE student SET stuName=''
```


-- 单张表的创建 约束 操作 select

-- 存储引擎

-- 存储引擎(client --> DBMS -->

数据文件) 表使用哪种存储引擎 DBMS 数据文件写入

-- 查询出当前MYSQL服务器所有的存储引擎的类型

-- INNODB  MYSQL 5.0 以后默认的存储引擎  (支持事务 行级锁 外键)

-- 写数据的速度比较的慢(数据比较安全)

-- 一张数据默认有两个文件frm 存储当前数据表的结构 ibd 存储当前数据表的索引和数据

-- INNODB 表更加健壮 

-- MYISAM 存储数据表使用三个文件(frm 存储当前数据表的结构,MYD(存储当前数据表的数据) MYI(存储当前数据表中数据的索引))

-- 不支持事务,行级锁,也不支持外键

-- 速度快(操作的速度快 不够安全)
业务只需要对当前数据表的数据进行单表的操作

-- memory 数据表只有一个数据文件(frm 磁盘之存储数据表的结构)

-- (比MYISAM存储引擎的速度还要快,支持hash索引 数据存储内存(DBMS) mysql服务一重启 所有的数据全部消失)

-- MRG_MYISAM MYISAM集合(实际的数据存储是在union的表中) 

-- BLACKHOLE 存储引擎只存储数据表结构 不存储任何的数据 数据存储服务器的二进制的日志中 (备份使用)

-- CSV 存储把数据存储在csv的数据文件中(数据表每一列不能为空 不支持自增长 不支持主键)

-- ARCHIVE 归档(数据文件进行压缩) 占用的磁盘空间比较小

-- 

```
SHOW ENGINES

CALL proc(10000)

SELECT COUNT(stuId) FROM student
```

-- 查询出当前数据表创建时使用的命令

```
SHOW CREATE TABLE student

CREATE TABLE `student` (
  `stuId` INT(11) NOT NULL AUTO_INCREMENT,
  `stuName` VARCHAR(20) DEFAULT NULL,
  `stuSex` ENUM('男','女') DEFAULT NULL,
  `stuAge` INT(11) DEFAULT NULL,
  PRIMARY KEY (`stuId`)
) ENGINE=INNODB AUTO_INCREMENT=567 DEFAULT CHARSET=utf8
```


-- 修改当前表的存储引擎

```
ALTER TABLE student ENGINE=MYISAM

DROP TABLE student
```


-- 创建一个数据表 存储引擎使用MYISAM

```
CREATE TABLE student(
    stuId INT AUTO_INCREMENT,
    stuName VARCHAR(20),
    stuSex ENUM('男','女'),
    stuAge INT,
    PRIMARY KEY(stuId)
)ENGINE=MYISAM


INSERT INTO student VALUES(DEFAULT,'张三','男',22)

SELECT * FROM student

SELECT COUNT(stuId) FROM student

TRUNCATE TABLE student

CALL proc(10000)

SELECT COUNT(stuId) FROM student

CREATE TABLE class(
   classId INT AUTO_INCREMENT,
   className VARCHAR(20),
   PRIMARY KEY(classId)
)ENGINE=MYISAM

ALTER TABLE student ADD classId INT

ALTER TABLE student ADD CONSTRAINT FK_CLASSID FOREIGN KEY(classID) REFERENCES class(classId)

INSERT INTO student VALUES()

DESC student

DROP TABLE student,class

CREATE TABLE student(
   stuId INT AUTO_INCREMENT,
   stuName VARCHAR(20),
   stuSex ENUM('男','女'),
   stuAge INT,
   PRIMARY KEY(stuId)
)ENGINE=MEMORY

SHOW TABLES

CREATE TABLE log_2017
(
    id INT,
    log_text TEXT,
    PRIMARY KEY(id)
)ENGINE=MYISAM


CREATE TABLE log_2018
(
    id INT,
    log_text TEXT,
    PRIMARY KEY(id)
)ENGINE=MYISAM

-- 
CREATE TABLE logtab(
   id INT,
   log_text TEXT,
   PRIMARY KEY(id)
)ENGINE=MRG_MYISAM UNION(log_2017,log_2018) INSERT_METHOD=FIRST

DROP TABLE logtab

INSERT INTO logtab VALUES(1,'操作删除')

SELECT * FROM logtab

SELECT * FROM log_2017

SELECT * FROM log_2018

SHOW TABLES

CREATE TABLE student(
    stuId INT AUTO_INCREMENT,
    stuName VARCHAR(20),
    stuSex ENUM('男','女'),
    stuAge INT,
    PRIMARY KEY(stuId)
)

ALTER TABLE student ENGINE=MYISAM

CALL proc(100000)


CREATE TABLE student1(
  stuId INT AUTO_INCREMENT,
  stuName VARCHAR(20),
  stuSex ENUM('男','女'),
  stuAge INT,
  PRIMARY KEY(stuId)
)ENGINE=ARCHIVE

INSERT INTO student1 SELECT * FROM student

SELECT COUNT(stuId) FROM student

SELECT COUNT(stuId) FROM student1

SHOW TABLES

DROP TABLE student

CREATE TABLE student(
   stuId INT NOT NULL,
   stuName VARCHAR(20) NOT NULL,
   stuSex ENUM('男','女') NOT NULL,
   stuAge INT NOT NULL
)ENGINE=CSV

INSERT INTO student VALUES(2,'admin','男',22)

SELECT * FROM student

CHECK TABLE student

REPAIR TABLE student

FLUSH TABLES


ALTER TABLE student ENGINE=BLACKHOLE
```


-- 多张表
-- 关系(多张表的关系) 主外键
-- 人  身份证  一对一关系
-- 学生  班级  一对多关系
-- 商品  订单  多对多关系

-- 外键
-- 主键表

```
CREATE TABLE class(
   classId INT AUTO_INCREMENT,
   className VARCHAR(20),
   PRIMARY KEY(classId)
)
```


-- 外键一定定义多方

-- 外键表(外键存储的表)

```
CREATE TABLE student(
   stuId INT AUTO_INCREMENT,
   stuName VARCHAR(20),
   stuSex ENUM('男','女')  ,
   stuAge INT,
   cid INT FOREIGN KEY REFERENCES class(classId),
   PRIMARY KEY(stuId),
   CONSTRAINT FK_CID FOREIGN KEY(cid) REFERENCES class(classId)
)
SELECT * FROM class
INSERT INTO class VALUES(1,'100001')
INSERT INTO class VALUES(2,'100002')
```

-- 外键的约束(外键的值必须是引用的主键表中存在的数据)(外键是允许为空)(外键允许重复)

```
INSERT INTO student VALUES(DEFAULT,'张三','男',22,1)
INSERT INTO student VALUES(DEFAULT,'李四','女',22,2)

DELETE FROM student WHERE stuId=2

SELECT * FROM student
```

-- 给外键添加唯一性约束

```
ALTER TABLE student ADD CONSTRAINT UQ_CID UNIQUE(cid)
```


-- 删除外键约束
ALTER TABLE student DROP FOREIGN KEY FK_CID
-- 删除唯一性约束

```
ALTER TABLE student DROP INDEX UQ_CID

ALTER TABLE student DROP COLUMN cid

DESC student

DELETE FROM student
```

-- 给stuId添加外键约束(注意:stuId本身主键 stuId列上有两个约束 主键 外键)

-- 主键 不能为空  唯一性

-- 外键 必须是主键表存在的数据

```
ALTER TABLE student ADD CONSTRAINT FK_STUID FOREIGN KEY(stuId) REFERENCES class(classId)

INSERT INTO student VALUES(2,'张三','男',22)

DESC student
```
