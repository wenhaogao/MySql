USE test

SHOW TABLES


-- 创建表的时候(类的数据类型 1:占用最小化 2:尽量避免的脏数据)
-- enum 占用一个字节

```
CREATE TABLE student(
   stuId INT,
   stuName VARCHAR(20),
   stuSex ENUM('男','女'),
   stuAge INT
)
```



```
INSERT INTO student VALUES(3,'张三','男',22)
```


-- 数据冗余


```
DROP TABLE student
-- 单列主键
CREATE TABLE student(
    stuId INT,
    stuName VARCHAR(20),
    stuSex ENUM('男','女'),
    stuAge INT,
    -- 定义主键(parmary key主键的标识 (stuId) 当前表的那一列为主键)
    PRIMARY KEY(stuId)
)
```

-- 组合主键(联合主键 不推荐使用)

```
PRIMARY KEY(stuId,stuName)

SELECT * FROM student
```


-- 查询当前数据库中的所有的数据表

```
SHOW TABLES

DESC student

INSERT INTO student VALUES(4,NULL,NULL,NULL)
INSERT INTO student VALUES(5,DEFAULT,DEFAULT,DEFAULT)
```

-- 添加列的默认值

```
CREATE TABLE student(
    stuId INT,
    stuName VARCHAR(20) DEFAULT '这个家伙很懒什么都没有留下',
    stuSex ENUM('男','女'),
    stuAge INT,
    -- 定义主键(parmary key主键的标识 (stuId) 当前表的那一列为主键)
    PRIMARY KEY(stuId)
)
```

-- 添加列的不能为空

```
CREATE TABLE student(
    stuId INT,
    stuName VARCHAR(20) NOT NULL DEFAULT '这个家伙很懒什么都没有留下',
    stuSex ENUM('男','女'),
    stuAge INT,
    -- 定义主键(parmary key主键的标识 (stuId) 当前表的那一列为主键)
    PRIMARY KEY(stuId)
)
```

-- 默认值和not null 属于列的属性

-- 修改数据表的名称

```
ALTER TABLE student RENAME stu
```


-- 修改表 添加列的信息

```
ALTER TABLE student ADD stuaddress VARCHAR(20)
```

-- 修改表 添加列的信息 (指定添加的列的位置)

```
ALTER TABLE student ADD stuadd TEXT AFTER stuName
```

-- 修改表添加列的信息(指定列的不能为空以及默认值)

```
ALTER TABLE student ADD stuadd VARCHAR(20) NOT NULL DEFAULT '陕西西安'
```

-- 修改表删除列的信息

```
ALTER TABLE student DROP COLUMN stuadd
```

-- 修改列的信息(修改列的名称和列的数据类型)

```
ALTER TABLE student CHANGE COLUMN stuaddress stuadd VARCHAR(20)
```


-- 修改表的信息(修改表的列的数据类型)

```
ALTER TABLE student CHANGE COLUMN stuadd stuadd VARCHAR(200)
```

-- 修改表的信息(修改表的列的默认值)

```
ALTER TABLE student CHANGE COLUMN stuadd stuadd VARCHAR(20) DEFAULT '陕西西安'
```


-- 主键 创建表 表的操作
-- 主键 自增长(当前类是主键 数据类型是数值类) 
-- 修改表给当前的类添加自动增长属性(属性)

```
ALTER TABLE student CHANGE COLUMN stuId stuId INT AUTO_INCREMENT
```

-- 插入数据的时候取当前的自动增长的值

```
INSERT INTO student VALUES(DEFAULT,'张三','男',22,'陕西西安')
```


-- 修改了自增长的初始值

```
INSERT INTO student VALUES(100,'李四','女',33,'上海浦东')
```


```
INSERT INTO student VALUES(DEFAULT,'张无忌','男',23,'北京朝阳')
```

-- 显示的添加主键的值 添加成功 但是主键的值永远保持上一次最大值(下一次自增长保持最大值加一)

```
INSERT INTO student VALUES(50,'张三丰','男',66,'陕西临潼')
```


-- 约束(保障当前表的数据的完整性)
-- 域的完整性(当前列的约束)
-- 默认值 检查约束(mysql不支持)
-- 实体完整性(行的约束)
-- 主键约束
-- 1:创建表的时候添加主键
-- 在列的定义的时候就添加主键

```
CREATE TABLE student(
   stuId INT PRIMARY KEY AUTO_INCREMENT,
   stuName VARCHAR(20) NOT NULL,
   stuSex ENUM('男','女'),
   stuAge INT
)
```

-- 2:创建表的时候添加主键但是单独添加主键

```
CREATE TABLE student(
    stuId INT AUTO_INCREMENT,
    stuName VARCHAR(20),
    stuSex ENUM('男','女'),
    stuAge INT,
    PRIMARY KEY(stuId)
)
```


-- 3:修改表的方式添加主键

```
CREATE TABLE student(
    -- 自动增长列必须是主键
    stuId INT ,
    stuName VARCHAR(20) NOT NULL,
    stuSex ENUM('男','女'),
    stuAge INT
)
```

-- 修改表添加表的主键

```
ALTER TABLE student ADD PRIMARY KEY(stuId)
```

-- 修改表删除表的主键

```
ALTER TABLE student DROP PRIMARY KEY
```

-- 修改表 添加约束 约束的名称为PK_STUID 主键约束

```
ALTER TABLE student ADD CONSTRAINT PK_STUID PRIMARY KEY(stuId)
```

-- 修改表 给主键列添加自动增长列的属性
ALTER TABLE student CHANGE COLUMN stuId stuId INT AUTO_INCREMENT


-- 唯一性约束(保证当前列的值是唯一存在)
-- 1:在创建表的时候定义到列的属性后

```
CREATE TABLE student(
   stuId INT AUTO_INCREMENT,
   stuName VARCHAR(20) NOT NULL UNIQUE,
   stuSex ENUM('男','女'),
   stuAge INT,
   PRIMARY KEY(stuId)
)
```

-- 创建表 单独创建约束

```
CREATE TABLE student(
   stuId INT AUTO_INCREMENT,
   stuName VARCHAR(20) NOT NULL,
   stuSex ENUM('男','女'),
   stuAge INT,
   PRIMARY KEY(stuId),
   UNIQUE(stuName)
)
```

-- 创建表时添加约束 

```
CREATE TABLE student(
   stuId INT AUTO_INCREMENT,
   stuName VARCHAR(20) NOT NULL,
   stuSex ENUM('男','女'),
   stuAge INT,
   PRIMARY KEY(stuId),
   CONSTRAINT UQ_STUNAME UNIQUE(stuName)
)

CREATE TABLE student(
   stuId INT AUTO_INCREMENT,
   stuName VARCHAR(20) NOT NULL,
   stuSex ENUM('男','女'),
   stuAge INT,
   PRIMARY KEY(stuId)
)

CREATE TABLE student(
   stuId INT AUTO_INCREMENT,
   stuName VARCHAR(20) NOT NULL,
   stuSex ENUM('男','女'),
   stuAge INT,
   PRIMARY KEY(stuId)
)

ALTER TABLE student ADD CONSTRAINT UQ_STUNAME UNIQUE(stuName)
```


-- 检查约束

-- 外键约束(表与表之间的关系)


```
DROP TABLE student

DELETE FROM student

DESC student

DROP TABLE student

SELECT * FROM student
```
