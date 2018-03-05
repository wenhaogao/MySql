CREATE DATABASE Test

USE Test

CREATE TABLE users(
   userId INT,
   userName VARCHAR(200),
   userPwd VARCHAR(200),
   PRIMARY KEY(userId)
)

CREATE TABLE userDemo AS SELECT * FROM users

-- mysql 触发器必须是行级触发器
DELIMITER //
CREATE TRIGGER trigger_name BEFORE INSERT ON users FOR EACH ROW
BEGIN
  INSERT INTO userdemo VALUES(new.userId,new.userName,new.userPwd);
END //

INSERT INTO users VALUES(1,'admin',PASSWORD('a111'))

SELECT * FROM users

SELECT * FROM userdemo

DROP TRIGGER trigger_name

DROP TABLE userDemo


CREATE TABLE people(
  id INT,
  pname VARCHAR(20),
  sex CHAR(6),
  age INT,
  PRIMARY KEY(id),
  CONSTRAINT UQ_PNAME UNIQUE(pname)
)

INSERT INTO people VALUES(1,'小米','男',20),(2,'小胖','女',25)

DROP TABLE people


CREATE TABLE people(
  id INT,
  pname VARCHAR(20),
  sex CHAR(6),
  age INT,
  PRIMARY KEY(id)
)

CREATE UNIQUE INDEX UQ_PNAME ON people(pname)

INSERT INTO people VALUES(3,'小米','男',20)

DROP TABLE people

DROP TABLE users

CREATE TABLE student(
   sid INT,
   stuName VARCHAR(20),
   stuSex CHAR(6),
   stuAge INT ,
   PRIMARY KEY(sid)
)

CREATE TABLE Teacher(
   tid INT,
   teacherName VARCHAR(20),
   PRIMARY KEY (tid)
)

CREATE TABLE Course(
   cid INT,
   courseName VARCHAR(20),
   tid INT,
   PRIMARY KEY(cid),
   CONSTRAINT FK_TID FOREIGN KEY(cid) REFERENCES teacher(tid)
)

CREATE TABLE Score(
   sid INT,
   cid INT,
   score DECIMAL(3,1),
   PRIMARY KEY(sid,cid),
   CONSTRAINT FK_SID FOREIGN KEY(sid) REFERENCES student(sid),
   CONSTRAINT FK_CID FOREIGN KEY(cid) REFERENCES (cid)
)

INSERT INTO student VALUES
(1,'张三1','男',22),
(2,'张三2','女',23),
(3,'张三3','男',24),
(4,'张三4','女',25),
(5,'张三5','男',26),
(6,'张三6','女',27),
(7,'张三7','男',28)

SELECT * FROM student

INSERT INTO teacher VALUES
(1,'张三丰'),
(2,'殷天正'),
(3,'杨逍'),
(4,'范瑶')

INSERT INTO Course VALUES
(1,'太极拳',1),
(2,'鹰爪功',2),
(3,'乾坤大挪移',3),
(4,'九阳神功',4)

SELECT * FROM score
INSERT INTO score VALUES
(1,1,90),
(1,2,90),
(1,3,90),
(1,4,90),
(2,1,90),
(2,2,90),
(2,3,90),
(2,4,90),
(3,1,90),
(3,2,90),
(3,3,90),
(3,4,90),
(4,1,90),
(4,2,90),
(4,3,90),
(4,4,90),
(5,1,90),
(5,2,90),
(5,3,90),
(5,4,90),
(6,1,90),
(6,2,90),
(6,3,90),
(6,4,90),
(7,1,90),
(7,2,90),
(7,3,90),
(7,4,90)

SELECT * FROM score

UPDATE score SET score=score-sid-cid


-- 课程  成绩 

SELECT * FROM score WHERE cid IN(1,2) GROUP BY cid,sid

UPDATE score SET score=score+10 WHERE cid = 2 AND sid IN(1,2,3)

SELECT A.sid FROM (SELECT sid,score FROM score WHERE cid=2) A,
(SELECT sid,score FROM score WHERE cid=1) B WHERE A.sid=B.sid AND B.score>A.score

SELECT sc1.sid FROM score sc1 INNER JOIN score sc2 
	ON sc1.sid=sc2.sid WHERE sc1.cid=1 AND sc2.cid=2 AND sc1.score>sc2.score
	
	
SELECT sid,AVG(score) FROM score GROUP BY sid HAVING AVG(score)>60

SELECT sid,stuName FROM student WHERE sid IN(SELECT sid FROM score GROUP BY sid HAVING MAX(score)<60)

SELECT * FROM score

SELECT * FROM student

DELIMITER //
CREATE PROCEDURE proc()
BEGIN
 DECLARE stuId INT;
 DECLARE sname VARCHAR(20);
 DECLARE ssex CHAR(6);
 DECLARE sage INT;
 DECLARE num INT DEFAULT 1;
 DECLARE stuCur CURSOR FOR SELECT * FROM student;
 DECLARE CONTINUE HANDLER FOR NOT FOUND SET num:=0;
 OPEN stuCur;
 REPEAT
  FETCH stuCur INTO stuId,sname,ssex,sage;
  SELECT stuId,sname,ssex,sage;
  UNTIL num=0 END REPEAT;
  CLOSE stuCur;
 END //


-- 指针  
SELECT * FROM student 

-- 客户端的内存(Java ResultSet 指针(指向了结果集的首行之前) next()) 行级指针(一次只能指向一行 读取也是读取一行)
-- 数组 int[] nums[1,2,3,4,5,6,7,8] nums[2] 连续空间 nums 一片空间首地址 
-- 游标(行级指针) ResultSet 
-- 游标(数据类型 不是数据对象 int char)
-- 不支持sql块 (mysql 变量必须定义在begin 个 end 之间) 
DROP PROCEDURE IF EXISTS proc;
DELIMITER $
CREATE PROCEDURE proc()
BEGIN
  DECLARE stuId INT;
  DECLARE sname VARCHAR(20);
  DECLARE ssex CHAR(6);
  DECLARE sage INT;
  -- 定义游标(说明有一个游标的变量 指向一个结果集(结果集没有产生))
  DECLARE num INT DEFAULT 1;
  DECLARE stuCursor CURSOR FOR SELECT * FROM student;
  -- 定义一个游标事件(事件的类型是游标找不到数据 则 num等于0)
  DECLARE CONTINUE HANDLER FOR NOT FOUND SET num:=0;
  -- open 以后 实际产生结果集 游标指向首地址之前
  OPEN stuCursor;
  -- 提取数据存入到指定的变量中
  REPEAT
  FETCH stuCursor INTO stuId,sname,ssex,sage;
  -- 输出
  SELECT stuId,sname,ssex,sage;
  UNTIL num=0 END REPEAT;
  CLOSE stuCursor;
END $


CALL proc()


DROP PROCEDURE IF EXISTS proc;
DELIMITER $
CREATE PROCEDURE proc()
BEGIN
  DECLARE stuId INT;
  DECLARE sname VARCHAR(20);
  DECLARE ssex CHAR(6);
  DECLARE sage INT;
  DECLARE stuCursor CURSOR FOR SELECT * FROM student;
  -- open 以后 实际产生结果集 游标指向首地址之前
  OPEN stuCursor;
  -- 提取数据存入到指定的变量中
  FETCH stuCursor INTO stuId,sname,ssex,sage;
  -- 输出
  SELECT stuId,sname,ssex,sage;
  FETCH stuCursor INTO stuId,sname,ssex,sage;
  -- 输出
  SELECT stuId,sname,ssex,sage;
  FETCH stuCursor INTO stuId,sname,ssex,sage;
  -- 输出
  SELECT stuId,sname,ssex,sage;
  FETCH stuCursor INTO stuId,sname,ssex,sage;
  -- 输出
  SELECT stuId,sname,ssex,sage;
  FETCH stuCursor INTO stuId,sname,ssex,sage;
  -- 输出
  SELECT stuId,sname,ssex,sage;
  FETCH stuCursor INTO stuId,sname,ssex,sage;
  -- 输出
  SELECT stuId,sname,ssex,sage;
  FETCH stuCursor INTO stuId,sname,ssex,sage;
  -- 输出
  SELECT stuId,sname,ssex,sage;
  FETCH stuCursor INTO stuId,sname,ssex,sage;
  -- 输出
  SELECT stuId,sname,ssex,sage;
  CLOSE stuCursor;
END $

CALL proc()


SHOW TABLE STATUS FROM test

SHOW TABLE STATUS LIKE 'users'

SHOW ENGINES

SHOW VARIABLES LIKE 'default_storage_engine'

SELECT ENGINE  FROM information_schema.`ENGINES` WHERE transactions='yes'

CREATE TABLE users(
    userId INT(3) PRIMARY KEY,
    userName VARCHAR(20),
    stuId INT
)ENGINE=MYISAM

ALTER TABLE users ENGINE=INNODB


SELECT * FROM users

SELECT * FROM student

SELECT COUNT(sid) FROM student

SELECT COUNT(userId) FROM users

SELECT COUNT(tid) FROM teacher

SHOW TABLE STATUS LIKE 'student'

SHOW TABLE STATUS LIKE 'users'

CREATE TABLE teacher(
   tid INT,
   tname VARCHAR(20),
   tsex CHAR(4),
   tage INT
)

SHOW TABLE STATUS LIKE 'teacher'

ALTER TABLE teacher ENGINE=MEMORY



DESC student

DESC users

ALTER TABLE users DROP COLUMN stuId

ALTER TABLE users ADD userPwd VARCHAR(20)

ALTER TABLE users ADD userAge INT

-- 
TRUNCATE TABLE student

SELECT * FROM student

ALTER TABLE student CHANGE COLUMN sid sid INT NOT NULL
INSERT INTO student VALUES(2,'张三','男',22)

DROP TABLE student

SELECT * FROM student

-- primary key 用于标识当前列的为主键列
CREATE TABLE student(
   stuid INT PRIMARY KEY,
   stuname VARCHAR(20),
   stuSex CHAR(4),
   stuAge INT
)

CREATE TABLE student(
   stuid INT,
   stuname VARCHAR(20),
   stuSex CHAR(4),
   stuAge INT,
   PRIMARY KEY(stuid)
)

CREATE TABLE student(
   stuid INT,
   stuname VARCHAR(20),
   stuSex CHAR(4),
   stuAge INT
)

ALTER TABLE student ADD CONSTRAINT PK_STUID PRIMARY KEY(stuId)

DROP TABLE student

DESC student

DESC innodb_sys_indexes

SELECT * FROM innodb_sys_indexes

-- 约束(主要是用来保障数据的完整性(数据正确性(满足语法要求int) 满足业务需求))
-- 主键约束(不能为null 不能重复)
-- 唯一性约束(当前列值不能出现重复)

SHOW TABLES

CREATE TABLE student(
   stuId INT,
   stuName VARCHAR(20),
   -- 主键
   PRIMARY KEY(stuId),
   -- 添加约束(约束的类型是唯一 约束的列是stuName)
   CONSTRAINT UQ_STUNAME UNIQUE(stuName)
)

CREATE TABLE student(
   stuId INT PRIMARY KEY,
   -- 创建的列 添加一个唯一标识(唯一性约束)
   stuName VARCHAR(20) UNIQUE
)

CREATE TABLE student(
   stuId INT,
   stuName VARCHAR(20),
   PRIMARY KEY(stuId)
)
-- 唯一性约束
ALTER TABLE student ADD CONSTRAINT UQ_STUNAME UNIQUE(stuName)

-- 修改表 修改列的属性(not null )
ALTER TABLE student CHANGE COLUMN stuName stuName VARCHAR(20) NOT NULL

ALTER TABLE student CHANGE COLUMN stuName sname VARCHAR(20) NOT NULL

ALTER TABLE student CHANGE COLUMN stuName stuName VARCHAR(20) NOT NULL

-- 默认值(列的属性 not null)
CREATE TABLE student(
   stuId INT,
   stuName VARCHAR(20) DEFAULT '姓名' NOT NULL,
   PRIMARY KEY(stuId)
)


CREATE TABLE student(
   stuId INT,
   stuName VARCHAR(20) NOT NULL,
   PRIMARY KEY(stuId)
)

-- 主键  唯一 约束对象

-- 修改列的名称 数据类型 not null default
ALTER TABLE student CHANGE COLUMN stuName stuName VARCHAR(20) DEFAULT 'hello'

INSERT INTO student VALUES(1,DEFAULT)

-- 性别(男，女)
-- 枚举数据类型
CREATE TABLE student(
   stuId INT,
   stuName VARCHAR(20),
   stuSex ENUM('男','女'),
   test SET('Java','C++','C#','JavaScript'),
   PRIMARY KEY(stuId)
)

-- enum('','','') 取值范围只能是定义的值 一次只能取一个
-- set('','','','') 取值范围只能是定义的值 一次取多个 值使用一个'值,值'

-- 外键(多个表(两个表))

-- 数据表(面向对象 类) 三大范式(一个表只能存储一种类别数据)
-- 数据和数据之间存在关系(数据库模型中数据关系都是用主外键描述)
-- 主键 外键
-- 对应的关系(一对多 ) Student   class   多个Student对应一个class  Student N   class one
-- 外键一般都是在多方
-- 外键应该是在Student方(外键存储的表 外键表) 引用class数据  class 主键表

DROP TABLE student

CREATE TABLE class(
   classId INT,
   className VARCHAR(20),
   PRIMARY KEY(classId)
)

CREATE TABLE student(
   stuid INT,
   stuName VARCHAR(20),
   stuSex ENUM('男','女'),
   stuAge INT,
   classId INT,
   PRIMARY KEY(stuId),
   -- 约束 约束名称 约束的表达式 
   CONSTRAINT FK_CLASSID FOREIGN KEY(classId) REFERENCES class(classId)
)

DROP TABLE student

CREATE TABLE student(
   stuId INT PRIMARY KEY,
   stuName VARCHAR(20),
   stuSex ENUM('男','女'),
   stuAge INT,
   classId INT
)

ALTER TABLE student DROP PRIMARY KEY

ALTER TABLE student ADD CONSTRAINT FK_CLASSID FOREIGN KEY(classId) REFERENCES class(classId)

-- 外键约束(允许为null 必须是引用表存在的数据 外键可以重复)
-- 外键为空情况 当前记录和其他表没有关系

-- 添加外键表的数据时 外键一定要是存在的数据
-- 删除主键表的数据时，需要先解除关系


SELECT * FROM class

SELECT * FROM student

INSERT INTO class VALUES(1,'100001')

INSERT INTO student VALUES(1,'张三','男',22,1)

DELETE FROM student WHERE stuId=2

DELETE FROM class WHERE classId=1

DELETE FROM student WHERE classId=1

UPDATE student SET classId=NULL WHERE classId=1
UPDATE student SET classId=1 WHERE classId IS NULL

DELETE FROM student

-- 枚举类型取值是规定的范围(每次只能去一个值) 爱好()

INSERT INTO student VALUES(3,'张三','女','Java,C++,JavaScript')



SELECT * FROM student
DESC student

DROP TABLE student

INSERT INTO student VALUES(1,NULL)


SELECT * FROM student

-- select 
SELECT * FROM book

-- 指定列的查询(过滤列)
SELECT bookId,bookName FROM book

SELECT bookId FROM book

SELECT bookAuthor,bookPrice,bookId,bookName,bookInfo FROM book
-- 在结果集上添加固定列
SELECT bookId,bookName,bookPrice,bookAuthor,bookInfo, '北京大学'
 FROM book
-- 数值类型的数据+
SELECT bookId+bookPrice 编号价格 FROM book

-- 过滤行
SELECT * FROM book WHERE FALSE

SELECT * FROM book WHERE booKId=1

SELECT * FROM book WHERE bookId<>1

SELECT * FROM book WHERE bookId!=1

SELECT * FROM book WHERE bookId>10

SELECT * FROM book WHERE bookId<10

SELECT * FROM book WHERE bookId>=10

SELECT * FROM book WHERE bookId<=10

SELECT * FROM book WHERE (bookId%2)>0

SELECT * FROM book WHERE (bookId%2)=0

SELECT * FROM book WHERE bookId IN(1,3,5,7,9,11,13)

SELECT * FROM book WHERE bookId NOT IN(1,3,5,7,9,11,13)

SELECT * FROM book WHERE NOT (bookId%2)>0

SELECT * FROM book WHERE bookid BETWEEN 1 AND 10

SELECT * FROM book WHERE bookid NOT BETWEEN 1 AND 10

UPDATE book SET bookName = NULL WHERE bookId=11

SELECT * FROM book WHERE bookName IS NULL

SELECT * FROM book WHERE bookName IS NOT NULL

INSERT INTO book VALUES(41,'九阳神功','张无忌',1002.33,'测试数据')
-- _ 任意一个字符 % 任意多个任意字符
SELECT * FROM book WHERE bookAuthor LIKE '张%'

SELECT * FROM book WHERE bookAuthor NOT LIKE '张%'

SELECT * FROM users

SELECT * FROM users WHERE userName='admin' AND userPwd='abbb'

SELECT * FROM book WHERE bookId=1 OR bookName='Java权威指南2'

-- and or not && || !

-- 排序(下面的语句排序不固定 不保障有序)
SELECT * FROM book
-- 指定按照bookId排序 升序(asc) 升序可以省略
SELECT * FROM book ORDER BY bookId ASC

SELECT * FROM book ORDER BY bookId DESC

SELECT * FROM book ORDER BY bookAuthor,bookId DESC

INSERT INTO book VALUES(44,NULL,NULL,NULL,NULL)

-- 聚合函数(统计函数)
-- 统计book表的行数
-- 按照bookId统计(只要bookId不为null)
SELECT COUNT(bookId) FROM book
SELECT COUNT(bookName) FROM book
SELECT COUNT(bookAuthor) FROM book
-- 按照全部列统计 只要没有全部列都为null
SELECT COUNT(*) FROM book
-- 统计bookId列的值最大数据
SELECT MAX(bookId) FROM book
-- 统计bookId列的值最小值
SELECT MIN(bookId) FROM book
-- 统计bookId的和(全部加到一起)
SELECT SUM(bookId) FROM book
-- 统计bookId 平均值
SELECT AVG(bookId) FROM book

-- 过滤重复项
SELECT DISTINCT bookAuthor FROM book

-- 分组
SELECT *,COUNT(bookId),MAX(booKId) FROM book GROUP BY bookAuthor
-- 在分组之前先过滤到bookId<=10 的数据(bookId<=10 的数据不参与分组)
SELECT *,COUNT(bookId) FROM book WHERE bookId>10 GROUP BY bookAuthor

-- 查询作者的数据数量大于10本的信息
SELECT *,COUNT(bookid) FROM book GROUP BY bookAuthor HAVING COUNT(bookId)>10

-- 查询作者的书籍数量大于10本 并bookId>10 
SELECT *,COUNT(bookid) FROM book WHERE bookId>10 
	GROUP BY bookAuthor HAVING COUNT(bookId)>10
	
-- 编号 姓名 科目 成绩
--  1   张三 语文   90
--  2   张三 数学   98
    3   张三 英语   99
    4   李四 语文   80
    5   李四 数学   88
    6   李四 英语   99
    7   王五 语文   92
    8   王五 数学   95
    9   王五 英语   97
    
-- 查询出所有科目的成绩都在90以上的学员信息
SELECT * FROM score GROUP BY 姓名 HAVING MIN(成绩)>90


-- 基本的查询
-- 多表查询

DESC student

INSERT INTO class VALUES(3,'100003'),(4,'100004'),(5,'100005')

ALTER TABLE student ADD CONSTRAINT PK_STUID PRIMARY KEY(stuId)

SELECT * FROM student  

SELECT classId,COUNT(stuId) FROM student GROUP BY classId

SELECT * FROM student

-- auto_increment 标识自动增长

CREATE TABLE teacher(
   teacherId INT PRIMARY KEY AUTO_INCREMENT,
   teacherName VARCHAR(20)
)

INSERT INTO teacher VALUES(DEFAULT,'张三'),(DEFAULT,'李四'),(DEFAULT,'王五')

INSERT INTO teacher VALUES(10,'张三丰')

INSERT INTO teacher VALUES(50,'张三丰')

INSERT INTO teacher VALUES(DEFAULT,'张三')

DELETE FROM teacher WHERE teacherId>3

SELECT * FROM teacher

-- teacher class 1 多 

ALTER TABLE class ADD teacherId INT

SELECT * FROM class

UPDATE class SET teacherId=(classId%3)+1


INSERT INTO class VALUES(6,'100006',NULL)

INSERT INTO student VALUES(51,'张无忌51','女',66,NULL)

-- classId 外键   引用另一个实体的主键
-- 多表的查询 (条件查询)
SELECT student.stuId,student.stuName,student.stuSex,student.stuAge,
	class.className,
	teacher.teacherName
       FROM student,class,teacher
       WHERE student.classId=class.classid AND class.teacherId=teacher.teacherId
       ORDER BY student.stuId

-- 链接查询(1:内连接 inner join 查询的效率高于条件查询)
-- 内连接(取两张表的交集)
SELECT student.stuId,student.stuName,student.stuSex,student.stuAge,
	class.className,
	teacher.teacherName
       FROM student INNER JOIN class
       ON student.classId=class.classid
       INNER JOIN teacher
       ON class.teacherId=teacher.teacherId
       ORDER BY stuId
       
-- 2:外链接(左外链接 右外链接)
-- 左外链接(是以left outer join 左边的数据表为主表)
-- 主表中存在的数据都出现(如果主表中的数据和链接的数据不存在关系)显示null
SELECT student.stuId,student.stuName,student.stuSex,student.stuAge,
	class.className
       FROM student LEFT OUTER JOIN class
       ON student.classId=class.classid
       ORDER BY stuId    
       
-- 右外链接(以right outer join 右边的为主表)
SELECT student.stuId,student.stuName,student.stuSex,student.stuAge,
	class.className
       FROM student RIGHT OUTER JOIN class
       ON student.classId=class.classid
       ORDER BY stuId
       
-- 3:交叉链接(笛卡尔积)
SELECT student.stuId,student.stuName,student.stuSex,student.stuAge,
	class.className
       FROM student CROSS JOIN class
       
-- 子查询
-- 在一个查询中包含另外一个查询
-- 查询出100002 班级所有的学生信息
-- 已知条件是班级名称 需要数据学生信息

SELECT classId FROM class WHERE className='100002'

SELECT * FROM student WHERE classid=2

SELECT * FROM student WHERE 
	classId=(SELECT classId FROM class WHERE className='100002')

-- exists() 判断存在不存在 如果括号内的查询返回有数据 true

SELECT * FROM student WHERE EXISTS(SELECT * FROM class WHERE 1=2)

DROP TABLE IF EXISTS users

SELECT * FROM users

-- 查询teacherName=张三 学生信息
SELECT teacherId FROM teacher WHERE teacherName='张三'

SELECT classId FROM class WHERE teacherId=1

SELECT * FROM student WHERE classId=3

SELECT * FROM student WHERE classId=(
	SELECT classId FROM class WHERE teacherId=(
		SELECT teacherId  FROM teacher WHERE teacherName='张三'
	)
)

-- 显示页面  分页
SELECT * FROM student

-- 参数页数 取数据
-- 按照ID 删除 行号
SELECT COUNT(stuId) FROM student WHERE stuId BETWEEN 1 AND 10

DELETE FROM student WHERE stuId IN (2,12,23)
-- limit 0 开始的行号位置  10 取多少条记录
-- 分页查询
SELECT * FROM student LIMIT 20,10

-- 修改一条数据
UPDATE student SET stuName='',stuSex='' WHERE stuId=1
-- 修改全部数据 
UPDATE student SET stuName='',stuSex=''

-- 一次添加一行数据


-- 表复制(复制一张数据表和数据源表一致结构 数据也一致 约束不会复制)
CREATE TABLE student1 AS SELECT * FROM student

DROP TABLE student1

-- student() 修改列的数量和位置
INSERT INTO student() VALUES()
-- 一次添加多行数据
INSERT INTO student VALUES(),(),()
-- 一次添加多行数据(复制表中数据)(不复制表结构 要求目标数据表提前存在)
INSERT INTO student1 SELECT * FROM student
-- 能复制表结构还能复制表的数据(复制不了约束) 要求目标数据表提前不存在
CREATE TABLE student1 AS SELECT * FROM student

-- 删除(部分数据)
DELETE FROM student1 WHERE stuId BETWEEN 1 AND 10
-- 删除全部数据(删除数据)
DELETE FROM student1

-- 删除全部的数据(截断表)
TRUNCATE TABLE student1

DROP TABLE student1

-- 区别:性能高(truncate) 性能低(delete)
-- 不能回滚(truncate) 能回滚(delete)
-- 自增长回归初始值(truncate) 不能(delete)
-- truncate只能删除数据表全部数据   delete 可以删除部分 也可以删除全部

CREATE TABLE users(
   userId INT PRIMARY KEY AUTO_INCREMENT,
   userName VARCHAR(20),
   userPwd VARCHAR(20)
)

DELIMITER //
DROP PROCEDURE IF EXISTS proc //
CREATE PROCEDURE proc()
BEGIN
 DECLARE i INT DEFAULT 1;
 WHILE i<=10000 DO
  INSERT INTO users VALUES(DEFAULT,CONCAT('admin',i),'a111');
  SET i=i+1;
  END WHILE;
 END; //
 
 
 CALL proc()
 
 SELECT COUNT(userId) FROM users

SELECT * FROM users 
 
 SET autocommit=1
 
 DELETE FROM users
 
 INSERT INTO users VALUES(DEFAULT,'haha','a111')

TRUNCATE TABLE users 
 
 ROLLBACK
 
 -- 游标 视图 存储过程 索引 函数
 -- 系统函数
 
 -- 存储过程 本身存储服务器  Java方法
 -- call 存储过程  select 函数
 -- 触发器(触发器是一个特殊的存储过程 存储在服务器) 
 -- 特殊点:自动执行 
 -- 触发器分类:after触发器(监听的动作之后) before触发器(监听的动作之前)
 DROP TRIGGER IF EXISTS mytrigger;
 DELIMITER &&
 -- 创建触发器 触发器名称为mytrigger
 CREATE TRIGGER mytrigger
  -- 当前触发器是一个after 监听的动作是insert on 监听的对象
  AFTER INSERT ON book FOR EACH ROW
  BEGIN
    INSERT INTO book1 VALUES(1,'hello','张三',33,'测试数据');
  END&&
  
  -- 创建一个触发器监听book表的insert操作(after) 执行触发器
  
  
  -- 只要在book表发生insert操作 那么就会自动执行当前触发器


SELECT * FROM book
SELECT * FROM book1

INSERT INTO book VALUES(1,'你好','李四',33.44,'测试数据')


-- 在满足监听的对象上的操作 自动执行触发器

-- new old 

-- 在触发器开始执行的时候 触发器会自动根据监听的对象自动创建两种内存表 new old
-- new old 内存表的结构和当前监听的对象的结构保持一致

-- mytrigger 监听的对象时book  mytrigger执行中创建的new old 内存表结构应该是book保持一致
-- 当前触发器执行的时候创建 触发器执行完毕后销毁

-- new 存储是新进来的数据 insert update(目标数据修改完的数据) 存储在new内存表中
-- old 存储是源数据 delete update(被修改的数据)

-- 监听book表的insert 操作 用户insert book表 同时会备份一份到book1表中
DROP TRIGGER IF EXISTS mytrigger;
DELIMITER $$
CREATE TRIGGER mytrigger 
AFTER INSERT ON book FOR EACH ROW
BEGIN
 INSERT INTO book1 VALUES(new.bookId,new.bookName,new.bookAuthor,new.bookPrice,new.bookInfo);
END$$


DROP TRIGGER IF EXISTS mytrigger1;
DELIMITER $$
CREATE TRIGGER mytrigger1
AFTER DELETE ON book FOR EACH ROW
BEGIN
  DELETE FROM book1 WHERE bookId=old.bookId;
END$$


DROP TRIGGER IF EXISTS mytrigger1;
DELIMITER $$
CREATE TRIGGER mytrigger1
BEFORE DELETE ON book FOR EACH ROW
BEGIN
  DELETE FROM book1 WHERE bookId=old.bookId;
END$$

--
-- 银行的系统 账户表   张三 操作 取1000 -- 李四账户减少1000

-- 账户操作 数据

SELECT * FROM book

SELECT * FROM book1

INSERT INTO book VALUES(2,'hello','张三',33.44,'测试数据')

DELETE FROM book WHERE bookId=2
-- 数学函数
-- 返回指定数据的绝对值
SELECT ABS(-1)
-- 返回指定数据的二进制的标识
SELECT BIN(10)
-- 返回指定数据的八进制的表示
SELECT OCT(10)
-- 返回指定数据的十六进制的表示
SELECT HEX(16)
-- 返回指定数据的最小整数
SELECT CEILING(1.01)
-- 返回指定数据的最大整数
SELECT FLOOR(1.9999)
-- 返回指定数据集合中最大的数
SELECT GREATEST(1,2,3,400,5,6,100)
-- 返回指定数据集合中最小的数
SELECT LEAST(1,2,3,44,500)
-- 求第一参数的和第二个参数的余数
SELECT MOD(10,3)
-- 返回指定数据的平方根
SELECT SQRT(9)
-- 返回第一个参数的 第二参数的次方
SELECT POWER(3,5)


-- 字符串函数
-- 返回指定字符串的ascii码
SELECT ASCII('a')
-- 链接两个字符串为一个字符串
SELECT CONCAT('hello','world')
-- 替换第一个参数中从第二个参数开始长度为第三个参数结束的字符为第四个参数
SELECT INSERT('helloworld',3,5,'你好')
-- 把指定的字符串转换成小写
SELECT LCASE('ABCD')  
SELECT LOWER('ABCD')
-- 截取第一个参数中 从左边开始的第二个参数和字符
SELECT LEFT('hello',2)
-- 统计当前字符串的长度
SELECT LENGTH('hello')
-- 
SELECT LENGTH('        hello       ')
SELECT LENGTH(RTRIM('        hello       '))
-- 去掉指定字符串左边的空格
SELECT LTRIM('        hello       ')
-- 去掉指定字符串右边的空格
SELECT RTRIM('        hello       ')
-- 把第一个参数字符串重复输出第二个参数指定遍
SELECT REPEAT('hello',3)
-- 逆序指定的字符串
SELECT REVERSE('abcd')
-- 从右边取第一个字符的第二个参数的字符数
SELECT RIGHT('abcd',2)
-- 排序接口 TreeSet 对元素进行自然排序
-- Java 接口 Compareable --> 方法 compareTo() 大于 1 小于 -1  等于 0
-- 比较两个字符串的大小 
SELECT STRCMP('abcd','accd')
-- 把指定的字符串转换成小写
SELECT UPPER('abcd')


-- 日期函数
-- 返回系统当前的日期
SELECT CURDATE()
-- 返回当前系统的时间
SELECT CURTIME()
-- 在指定的日期上加上指定的天数
SELECT ADDDATE('2017-07-14',200)
-- 返回当前的系统的日期和时间
SELECT NOW()
-- 返回指定日期是当前月的第几天
SELECT DAYOFMONTH('2017-07-30')
-- 返回指定日期是当前年中第几天
SELECT DAYOFYEAR('2017-12-31')
SELECT DAYOFYEAR(CURDATE())
-- 当前的日期是当前周的第几天
SELECT DAYOFWEEK(CURDATE())
SELECT DAYOFWEEK('2017-12-30')
-- 取当前时间中的小时数
SELECT HOUR(CURTIME())
-- 取当前时间中的分钟数
SELECT MINUTE(CURTIME())
-- 取当前时间中的秒数
SELECT SECOND(CURTIME())
-- 返回当前日期是当前年的第几周
SELECT WEEK('2017-12-30')
-- 返回指定日期中的年份
SELECT YEAR(CURDATE())
-- 返回指定日期中的月份
SELECT MONTH(CURDATE())
-- 返回指定日期中的天数
SELECT DAY(CURDATE())


-- 加密函数
PASSWORD
MD5()

-- 系统函数
-- 查询出当前连接的数据库名称
SELECT DATABASE()
-- 查看当前连接的用户
SELECT USER()

-- 条件表达式
SELECT IF(1=2,2,3)


SELECT * FROM users

TRUNCATE TABLE users

SHOW COLUMNS FROM users

ALTER TABLE users CHANGE COLUMN userPwd userPwd VARCHAR(200)

INSERT INTO users VALUES(1,'admin',PASSWORD('a111'))

SELECT * FROM users WHERE userName='admin' AND userPwd=PASSWORD('a111')

INSERT INTO users VALUES(2,'admin1',MD5('b222'))

SELECT * FROM users WHERE userName='admin1' AND userPwd=PASSWORD('b222')

-- password  md5 加密都不可逆 

-- 事务 MYSQL 基本SQL语言 索引 存储过程 事务 存储引擎(MYISAM INNODB Memory Merge)
-- 什么是事务
-- 事务是一个执行单元 client --> DBMS -->  数据文件   DBMS 内存一个进程   数据文件磁盘文件
-- IO操作 IO操作性能比较差 数据缓冲区 DBMS中数据缓冲区 提交操作 影响数据文件
-- 事务
-- MYSQL 中自动提交事务 手动提交  默认都是采用的自动提交
-- 默认一条SQL指令为一个事务
-- 自动开启一个事务
DELETE FROM book WHERE bookId=1
-- 执行完毕自动提交事务

-- 事务特征:
   -- 1:原子性(一个事务为一个基本执行单元,不可分割)可以是多条语句
   -- 2:一致性(一个事务包含的所有的命令只会产生一个结果) 要么都成功要么都失败
   -- 3:永久性(事务操作的数据的结果保存到数据文件)
   -- 4:隔离性(事务和事务之间相互隔离) 事务支持并发 
   
-- 隔离级别
-- 查看当前服务器默认的事务隔离级别
-- 隔离级别分为四种:
-- 1 读未提交数据(READ UNCOMMITTED)  A 事务   B事务  A事务操作数据  B立即就能看见 脏读
-- 2:读已提交数据(READ COMMITTED) A 事务操作 B 事务 B事务中出现多次查询数据不一致 不可以重复读
-- 3:可重复读(REPEATABLE READ) 可重复读(确保在同一个事务中多次查询的数据一致) 幻读(A insert 数据并提交 B事务多次查询查不到A事务insert B事务有操作全部数据操作再次查询就会查询出A事务insert的数据)
-- 4:串行化(SERIALIZABLE) 事务的执行全部修改成同步 性能下降


-- 查看当前服务器默认的事务隔离级别
SELECT @@tx_isolation

-- 手动开启一个事务
START TRANSACTION
 INSERT INTO book VALUES(4,'Java权威指南2','张无忌',44.55,'测试数据');
  DELETE FROM book WHERE bookId>2
 UPDATE book SET bookName='乾坤大挪移' WHERE bookId=1
 
 -- 回滚事务
 ROLLBACK

 -- 提交事务
 COMMIT 
 
SELECT * FROM book
 
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED


-- 一个事务的结束 (1:提交(commit) 2:回滚(rollback))

-- 自动事务修改成手动事务
SET autocommit=0 

UPDATE book SET bookName='hello' WHERE bookId=1;

DELETE FROM book WHERE bookId=2

ROLLBACK

SELECT * FROM book

-- Java 程序连接数据库 JDBC 


-- 存储引擎
-- 基本增 删 查 改

-- 数据类型
-- 数值型
-- tinyint  1字节
-- smallint 2字节
-- mediumint 3字节
-- int integer  4字节
-- bigint 8字节

-- 浮点型
-- float 4字节
-- double 8字节
-- decimal(8,2) 8+2 字节

-- 字符型
-- char(4) 固定长度的字符串
-- varchar(4) 可变长度的字符串
-- tinytext 短文本 0-255个字符
-- text 文本 0-65535 字符
-- mediumtext 中等长度的文本(16777215)
-- longtext 大文本 
-- blob 二进制形式的文本数据
-- tinyblob
-- mediumblob
-- longblob

-- 日期型
-- date 4字节 (yyyy-mm-dd)
-- time 3字节(HH:MM:SS)
-- datetime(yyyy-mm-dd HH:mm:ss)
-- timestamp(yyyy-mm-dd HH:mm:ss)时间戳
-- year 1字节(yyyy)

-- 复合类型
-- enum(枚举)
-- set

-- 面向对象 对象-->类
-- 表只能存储一类数据 -->  类
-- 一行数据-->对象
-- 一列数据-->属性

-- 创建数据表(保证当前数据是描述一类数据)
-- 创建数据库(在一个服务器实例中数据库(逻辑)多个)
-- 关系型数据库都是以数据表为数据的管理单元
-- 显示当前数据库中所有的数据表
SHOW TABLES

-- 删除(被删除数据表不存在 则报错)
DROP TABLE book1
-- 删除 判断 是否存在 存在则删除
DROP TABLE IF EXISTS student;
CREATE TABLE student(
   stuId INT PRIMARY KEY AUTO_INCREMENT,
   stuName VARCHAR(6),
   stuSex ENUM('男','女'),
   stuAge TINYINT -- (4) 取值范围 显示宽度
)ENGINE=MYISAM AUTO_INCREMENT=100 DEFAULT CHARSET=utf8-- 指定当前表的自动增长的初始值


CREATE TABLE users(
  userId INT PRIMARY KEY AUTO_INCREMENT,
  userName VARCHAR(20),
  userPwd VARCHAR(20)
)

INSERT INTO users VALUES(DEFAULT,'admin','a111')

SELECT * FROM users

DELETE FROM student

INSERT INTO student VALUES(DEFAULT,'李四','女',22)

-- mysql 检查约束

INSERT INTO student VALUES(1,'张三','男',33)

SELECT * FROM student

-- 主键 身份证号码 (判别是否相同数据唯一标准)

-- 一张数据表必须要有主键 (数据表存储数据)

-- 主键 一定要保障唯一性 修改可能性最低 当前主体的业务无关 
-- PRIMARY KEY
-- 主键不能重复 主键不能为null
-- AUTO_INCREMENT 自动增长 默认从1开始 必须主键 必须是数值类型
-- 类 复合数据类型
-- User 类  userId  userName userPwd  
-- equals() 按照一种规则 判断是否相等  userId 

-- 学号
-- 身份证

-- 数据库的三大范式(创建数据表的需要遵循的规范)
-- 1:每一张数据表的列都要保障列的原子性 
-- id name 电话(没有保障原子性)  id name 家庭电话 工作电话
-- 第一范式:数据表的每一列都是不可再分
-- 2:主键能唯一标识当前表的所有非主键列(数据表的所有非主键列都依赖主键列)
-- stuId stuName stuSex stuAge className  stuId主键  stuId 能唯一标识className?
-- stuId stuName stuSex stuAge classId className
-- 3:主键能唯一标识当前表的所有非主键列 但不能传递依赖

-- 主键创建
-- 主键是一种约束(唯一,not null)
-- 查看当前表的结构
DESC student
-- 显示当前数据表的列的信息
SHOW COLUMNS FROM student

DROP TABLE IF EXISTS student;
CREATE TABLE student(
   stuId INT PRIMARY KEY AUTO_INCREMENT,
   stuName VARCHAR(20) NOT NULL,
   stuSex ENUM('男','女'),
   stuAge INT
)

DROP TABLE IF EXISTS student;
CREATE TABLE student(
    stuId INT AUTO_INCREMENT,
    stuName VARCHAR(20) NOT NULL,
    stuSex ENUM('男','女'),
    stuAge INT,
    PRIMARY KEY(stuId)
)

DROP TABLE IF EXISTS student;
CREATE TABLE student(
    stuId INT,
    stuName VARCHAR(20) NOT NULL,
    stuSex ENUM('男','女'),
    stuAge INT
)
-- 修改表给stuId添加主键约束
ALTER TABLE student ADD CONSTRAINT PK_STUID PRIMARY KEY(stuId)

-- 修改表给stuId添加自动增长
ALTER TABLE student CHANGE COLUMN stuId stuId INT AUTO_INCREMENT

-- 修改表的名称
ALTER TABLE student RENAME stu

ALTER TABLE stu RENAME student

-- 修改student表的存储引擎
ALTER TABLE student ENGINE=INNODB

-- 修改student表的默认字符集
ALTER TABLE student DEFAULT CHARSET=utf8

-- 修改student表的默认的自增长的初始值
ALTER TABLE student AUTO_INCREMENT=100

-- 修改表添加一个新列
ALTER TABLE student ADD stuAddress VARCHAR(200)

-- 修改表删除一列
ALTER TABLE student DROP COLUMN stuAddress

-- 修改表添加新列并指定位置
ALTER TABLE student ADD stuAddress VARCHAR(200) AFTER stuId

-- 修改表添加新列并添加到当前数据表第一列
ALTER TABLE student ADD stuAddress VARCHAR(200) FIRST

-- 修改数据表的指定列的名称
ALTER TABLE student CHANGE stuAddress stuAdd VARCHAR(200)

-- 修改数据表的指定类的数据类型
ALTER TABLE student CHANGE stuAdd stuAdd TEXT

-- 修改表指定的列不能为空
ALTER TABLE student CHANGE stuAdd stuAdd TEXT NOT NULL

-- 修改表给指定的类添加默认值
ALTER TABLE student CHANGE stuAdd stuAdd VARCHAR(200) NOT NULL DEFAULT '陕西‘西安'

-- 删除主键时候当前列不能是auto_increment
-- 去掉自动增长列
ALTER TABLE student CHANGE stuId stuId INT 
-- 删除主键
ALTER TABLE student DROP PRIMARY KEY

-- 约束(约束的值) 保障数据完整性的规范  完整性 数据的正确性 数据的业务
-- 主键约束
-- 唯一性约束
DROP TABLE IF EXISTS student;
CREATE TABLE student(
    stuId INT AUTO_INCREMENT,
    stuName VARCHAR(20) UNIQUE NOT NULL,
    stuSex ENUM('男','女'),
    stuAge INT,
    PRIMARY KEY(stuId)
)


DROP TABLE IF EXISTS student;
CREATE TABLE student(
    stuId INT AUTO_INCREMENT,
    stuName VARCHAR(20) NOT NULL,
    stuSex ENUM('男','女'),
    stuAge INT,
    PRIMARY KEY(stuId),
    CONSTRAINT UQ_STUNAME UNIQUE(stuName)
)

DROP TABLE IF EXISTS student;
CREATE TABLE student(
    stuId INT AUTO_INCREMENT,
    stuName VARCHAR(20) NOT NULL,
    stuSex ENUM('男','女'),
    stuAge INT,
    PRIMARY KEY(stuId)
)

ALTER TABLE student CHANGE COLUMN stuName stuName VARCHAR(20) UNIQUE

-- 删除唯一性约束(索引)
DROP INDEX stuname ON student
ALTER TABLE student DROP INDEX stuname

-- 查询当前数据表所有的约束
SELECT * FROM information_schema.`TABLE_CONSTRAINTS` WHERE table_name='student'


-- 表的范式(创建多张表) 不同的数据分割为不同的表
-- 外键约束(引用关系)  student class
-- 一对一  一对多  多对多

DESC student

CREATE TABLE class(
    classId INT AUTO_INCREMENT,
    className VARCHAR(20),
    PRIMARY KEY(classId)
)

-- 关系列(反应当前关系的描述)
-- 外键关系列在多方存在

-- 外键的创建
DROP TABLE IF EXISTS student;
CREATE TABLE student(
   stuId INT AUTO_INCREMENT,
   stuName VARCHAR(20) NOT NULL,
   stuSex ENUM('男','女'),
   stuAge INT,
   cid INT,
   PRIMARY KEY(stuId),
   CONSTRAINT FK_CID FOREIGN KEY(cid) REFERENCES class(classId),
   CONSTRAINT UQ_CID UNIQUE(cid)
)

DROP TABLE IF EXISTS student;
CREATE TABLE student(
   stuId INT AUTO_INCREMENT,
   stuName VARCHAR(20) NOT NULL,
   stuSex ENUM('男','女'),
   stuAge INT,
   cid INT,
   PRIMARY KEY(stuId)
)

ALTER TABLE student ADD CONSTRAINT FK_CID FOREIGN KEY(cid) REFERENCES class(classId)

-- 删除外键
ALTER TABLE student DROP FOREIGN KEY FK_CID


-- 默认的外键是一对多
-- 一对一(外键添加约束性约束)
DROP TABLE IF EXISTS student;
CREATE TABLE student(
   stuId INT,
   stuName VARCHAR(20) NOT NULL,
   stuSex ENUM('男','女'),
   stuAge INT,
   PRIMARY KEY(stuId),
   CONSTRAINT FOREIGN KEY(stuId) REFERENCES class(classId)
)

DESC student
stuId 主键 不能为null 不能重复
  外键 值必须是主键表存在的数据

SELECT * FROM class




SELECT * FROM class
INSERT INTO class VALUES(2,'100002')
SELECT * FROM student
-- 引用了一个不存在的主键表的数据(可以为null 可以重复)
INSERT INTO student VALUES(3,'张三','男',22)

INSERT INTO student VALUES(DEFAULT,'李四','男',22,1)


-- 检查约束(MYSQL 不支持)


-- 默认值 not null(列的属性)

INSERT INTO student VALUES(DEFAULT,'张三','男',22)

SELECT * FROM student

DESC student

-- 创建表 创建范式 数据类型 约束(主键约束，唯一性约束，外键约束(一对一，一对多实现 多对多))

-- DDL(create drop) DCL DML(insert delete update) DQL(select) TPL CCL 

-- DQL select 
-- 查询 所有的列 来自于xxx表
SELECT * FROM xxx

DROP TABLE IF EXISTS student;
CREATE TABLE student(
   stuId INT,
   stuName VARCHAR(20) NOT NULL,
   stuSex ENUM('男','女'),
   stuAge INT,
   PRIMARY KEY(stuId)
)

ALTER TABLE student CHANGE COLUMN stuName stuName VARCHAR(20) NULL

DESC student

-- 查询student表所有的数据 * 通配符
SELECT * FROM student

-- 查询指定列的数据
SELECT stuId FROM student

SELECT stuId,stuName,stuAge FROM student;

-- 改变列的别名
SELECT stuId 编号,stuName 姓名,stuSex 性别,stuAge 年龄 FROM student

-- 把两列的值相加
SELECT stuId+stuAge FROM student;

-- 过滤重复项(相同的值 去掉)
SELECT DISTINCT stuSex FROM student

-- 行过滤

SELECT * FROM student WHERE stuId=1

SELECT * FROM student WHERE stuId>10

SELECT * FROM student WHERE stuId>=10

SELECT * FROM student WHERE stuId<=10

SELECT * FROM student WHERE stuId != 1

SELECT * FROM student WHERE stuId <> 1

SELECT * FROM student WHERE stuName='张三2'

SELECT * FROM student WHERE stuSex='男'

SELECT * FROM student WHERE stuId IN (1,4,5,7,8,9)

SELECT * FROM student WHERE stuId NOT IN(1,2,3,4,5,6,7,8,9,10,11)

SELECT * FROM student WHERE stuId IS NULL

SELECT * FROM student WHERE stuId IS NOT NULL

SELECT * FROM student WHERE stuId BETWEEN 1 AND 20

SELECT * FROM student WHERE stuId NOT BETWEEN 1 AND 20
-- &&
SELECT * FROM student WHERE stuId=1 AND stuName='李四1' AND stuSex='男'

-- ||
SELECT * FROM student WHERE stuId=1 OR stuName='张三2'

-- !
SELECT * FROM student WHERE NOT stuId=1


-- _ 一个任意字符 % 任何个任意字符

SELECT * FROM student WHERE stuName LIKE '张三2'

SELECT * FROM student WHERE stuName LIKE '张%'

SELECT * FROM student WHERE stuName LIKE '张三_'

SELECT * FROM student WHERE stuName LIKE '张三%'

SELECT * FROM student WHERE stuName LIKE '张%'

-- 排序
-- asc 升序
SELECT * FROM student ORDER BY stuId ASC
-- 降序
SELECT * FROM student ORDER BY stuId DESC

SELECT * FROM student WHERE stuId>90

SELECT stuId,stuName,stuSex,stuAge FROM student WHERE stuId>90 ORDER BY stuName ASC

-- 排序列的数据出现重复

SELECT * FROM student WHERE stuId>90 ORDER BY stuName , stuID ASC

SELECT * FROM student ORDER BY stuName , stuID DESC

-- 主键(单列主键)

PRIMARY KEY(stuId)

-- 组合主键(联合主键) 不推荐使用
PRIMARY KEY(stuId,classId)

-- 分组
SELECT * FROM student GROUP BY stuSex

SELECT * FROM student GROUP BY stuSex,stuId

-- 分组前过滤数据
SELECT * FROM student WHERE stuId>10 GROUP BY stuSex

-- having 分组完筛选
SELECT * FROM student WHERE stuId>10 GROUP BY stuSex HAVING 条件

UPDATE student SET stuName=NULL WHERE stuId=100
UPDATE student SET stuSex='男' WHERE stuId>90
-- 统计(聚合函数) count  sum  avg  max  min
-- count(列) 列去除null
SELECT COUNT(stuName) FROM student

SELECT COUNT(*) FROM student

SELECT stuSex,COUNT(stuId) FROM student GROUP BY stuSex HAVING COUNT(stuId)>50

-- sum
SELECT SUM(stuId) FROM student

SELECT SUM(stuName) FROM student

-- 平均数
SELECT AVG(stuId) FROM student

SELECT MAX(stuId) FROM student

SELECT MIN(stuId) FROM student

ALTER TABLE student CHANGE stuAge stuAge INT(3)

-- ID Name 科目 成绩
   1  张三 语文  99
   2  张三 数学  90
   3  张三 英语  89
   4  李四 语文  99
   5  李四 数学  92
   6  李四 英语  99
   7  王五 语文  99
   8  王五 数学  90
   9  王五 英语  93
   
 -- 求出所有科目成绩都在90(包含)以上的人姓名
  
 -- enum('1','2','3') 1 2 3
 -- set('1','2','3') '1,2,3'
 
 CREATE TABLE demo(
    did INT PRIMARY KEY,
    dname SET('a','b','c','d') 
 )
 
 INSERT INTO demo VALUES(4,'a,d,c')
 
 SELECT * FROM demo
 
 DROP TABLE demo
 
 
 
 CREATE TABLE test(
    id INT AUTO_INCREMENT,
    stuname VARCHAR(20),
    subjec ENUM('数学','语文','英语'),
    score DECIMAL(5,2)  ,
    PRIMARY KEY(id)
 )

ALTER TABLE test CHANGE score score DECIMAL(5,2) 
 
 INSERT INTO test VALUES(7,'王五','数学',92);
 INSERT INTO test VALUES(8,'王五','语文',100);
 INSERT INTO test VALUES(9,'王五','英语',96);
 
 SELECT * FROM test
 
 SELECT stuname FROM test 
 
 SELECT stuname FROM test GROUP BY stuname HAVING MIN(score)>=90
 
 SELECT stuname,MIN(score) FROM test GROUP BY stuName

 DROP TABLE IF EXISTS test 

 DROP TABLE users
 
 DESC student

SELECT * FROM student 

DELETE FROM student
 
 SHOW TABLES

 
 SELECT * FROM student

SELECT stuAge,stuSex,stuId,stuName FROM student

SELECT * FROM student 
 
 -- 增加
 INSERT INTO student(默认按照建表的顺序定义) VALUES(1,'张三','男',22)
 
 INSERT INTO student(stuId,stuName,stuSex,stuAge) VALUES(2,'李四','女',23)
 
 INSERT INTO student(stuAge,stuName,stuId,stuSex) VALUES(24,'王五',3,'男')

 INSERT INTO student(stuId,stuName) VALUES(8,'张三')
 
 -- 一次添加多行
 INSERT INTO student VALUES
 (4,'张三丰1','男',25) ,
 (5,'张三丰2','女',25) ,
 (6,'张三丰3','男',25) ,
 (7,'张三丰4','女',25) 
 
 -- 表复制
 -- 表结构的复制(不会复制数据)(约束复制)(目标数据表不存在)
 CREATE TABLE student1 LIKE student
 -- 表结构和数据同时复制(要求新数据表不存在)
 -- 但是不会复制约束
 CREATE TABLE student1 AS SELECT * FROM student
 CREATE TABLE stduent2 AS SELECT stuId,stuName FROM student WHERE stuId<5 

 -- 只复制表的数据不会复制表结构(要求数据表存在)
 CREATE TABLE student2 LIKE student
 -- 查询出来的student表的数据全部insert到student2中
 INSERT INTO student2 SELECT * FROM student
 
 
 SELECT * FROM student1
 SELECT * FROM stduent2

 DROP TABLE IF EXISTS stduent2 
 
 DROP TABLE IF EXISTS student1
 
 INSERT INTO student1 VALUES(1,'张翠山','男',26)
 
 -- 更新
 UPDATE student SET stuName='张无忌',stuAge=stuAge+10 WHERE stuId=1
 
 SELECT * FROM student
 
 UPDATE student SET stuName='张翠山'
 
 -- 删除
 DELETE FROM student WHERE stuId BETWEEN 1 AND 10
 
 DELETE FROM student WHERE stuSex IS NULL
 
 DELETE FROM student
 -- 截断表(和delete from student 一样都是清空当前数据表所有的数据)
 TRUNCATE TABLE student
 
 -- 1:delete 可回滚  truncate 不可以回滚  truncate 性能高于 delete
 -- 设置当前命令手动提交
 SET autocommit=0
 
 DELETE FROM student
 
 SELECT * FROM student
 
 ROLLBACK
 
 TRUNCATE TABLE student

 -- delete 不会让自增长初始化 
 -- truncate 会让自增长初始化

 CALL proc() 
 
 ALTER TABLE student CHANGE stuId stuId INT AUTO_INCREMENT
 
 SELECT * FROM student
 
 DELETE FROM student
 
 COMMIT
 
 INSERT INTO student VALUES(DEFAULT,'张三','男',22)
 
 TRUNCATE TABLE student
 
 
 CREATE TABLE class(
   classId INT AUTO_INCREMENT,
   className VARCHAR(20) NOT NULL,
   PRIMARY KEY(classId) 
 )
 DROP TABLE IF EXISTS student;
 CREATE TABLE student(
   stuId INT AUTO_INCREMENT,
   stuName VARCHAR(20) NOT NULL,
   stuSex ENUM('男','女') ,
   stuAge INT,
   PRIMARY KEY(stuId)
 )

 ALTER TABLE student ADD cid INT

 ALTER TABLE student ADD CONSTRAINT FK_CID FOREIGN KEY(cid) 
	REFERENCES class(classId) ON DELETE RESTRICT ON UPDATE RESTRICT

 ALTER TABLE student ADD CONSTRAINT FK_CID FOREIGN KEY(cid) 
	REFERENCES class(classId) ON DELETE CASCADE ON UPDATE CASCADE
 
 ALTER TABLE student ADD CONSTRAINT FK_CID FOREIGN KEY(cid) 
	REFERENCES class(classId) ON DELETE SET NULL ON UPDATE SET NULL

 DESC student 
 
 INSERT INTO class VALUES(1,'100001'),(2,'100002'),(3,'100003')

 UPDATE  
 
 INSERT INTO student VALUES
 (1,'张三1','男',22,1),
 (2,'张三1','男',22,1),
 (3,'张三1','男',22,1),
 (4,'张三1','男',22,2),
 (5,'张三1','男',22,2),
 (6,'张三1','男',22,2),
 (7,'张三1','男',22,3),
 (8,'张三1','男',22,3),
 (9,'张三1','男',22,3)
 
 UPDATE student SET stuName=CONCAT(LEFT(stuName,2),stuId),stuSex=IF(MOD(stuId,2)=0,'男','女'),stuAge=stuAge+stuId
 
 SELECT * FROM class
 
 DELETE FROM class WHERE classId=2
 
 -- 多表的查询(级连，子查询)
 -- 级连 (级连的作用)
 -- 级连(多张表 具有关系 主外键)
 -- insert 外键表的数据 外键表的关系列 必须引用一个存在的主键表的数据
 -- delete 主键表 需要(把外键表关联的数据删除 把引用关系取消(外键关系列null))

 
 INSERT INTO class VALUES(1,'1000001')
 
 INSERT INTO student VALUES(1,'张三','男',22,1)

 -- 1 : 删除外键表存在需要删除主键表数据的引用的数据
 DELETE FROM student WHERE cid=1
 DELETE FROM student WHERE stuId=1 
 -- 2:外键引用设置null
 UPDATE student SET cid=NULL WHERE cid=1
 DELETE FROM class WHERE classId=1
 
 SELECT * FROM student
 SELECT * FROM class
 
 SHOW TABLES
 -- cascade (级连)
 -- 两张表必须存在关系
 -- 外键必须是key(索引)(创建了外键默认就是key)
 -- 外键使用cascade
 
 DROP TABLE IF EXISTS student
 DROP TABLE IF EXISTS class
 
 CREATE TABLE class(
    classId INT AUTO_INCREMENT,
    className VARCHAR(20) NOT NULL, 
    PRIMARY KEY(classId)
 )
 
 CREATE TABLE student(
    stuId INT AUTO_INCREMENT,
    stuName VARCHAR(20) NOT NULL, 
    stuSex ENUM('男','女'), -- 选项固定的情况下一般都是enum char varchar
    stuAge INT,
    PRIMARY KEY(stuId)
 )
 -- 添加外键列
 ALTER TABLE student ADD cid INT

-- RESTRICT 标识关系列不能维护 
 
 -- 修改表添加外键约束 同时添加级连删除
 ALTER TABLE student ADD CONSTRAINT FK_CID 
	FOREIGN KEY(cid) REFERENCES class(classId) ON DELETE CASCADE
	
 ALTER TABLE student ADD CONSTRAINT FK_CID 
	FOREIGN KEY(cid) REFERENCES class(classId) ON UPDATE CASCADE

 ALTER TABLE student ADD CONSTRAINT FK_CID 
	FOREIGN KEY(cid) REFERENCES class(classId) ON DELETE SET NULL
	
 ALTER TABLE student ADD CONSTRAINT FK_CID 
	FOREIGN KEY(cid) REFERENCES class(classId) ON UPDATE SET NULL
	
 ALTER TABLE student DROP FOREIGN KEY FK_CID
	

 INSERT INTO class VALUES(1,'100001'),(2,'100002'),(3,'100003'),(4,'100004'),(5,'100005')
 INSERT INTO student VALUES(1,'张三','男',22,1)
 
 DELETE FROM class WHERE classId=1
 
 SELECT * FROM class
 SELECT * FROM student
 UPDATE student SET cid=1

 DELETE FROM class 
 DELETE FROM student
 
 UPDATE class SET classId=2 WHERE classId=1
 
 DELETE FROM class WHERE classId=2
 
 DROP PROCEDURE IF EXISTS proc;
 DELIMITER $$
 CREATE PROCEDURE proc()
 BEGIN
   DECLARE i INT DEFAULT 1;
   WHILE i<=50 DO
     IF MOD(i,2)=0 THEN
       INSERT INTO student VALUES(i,CONCAT('张三',i),'男',22+i,IF(MOD(i,5)=0,5,MOD(i,5)));
     ELSE
       INSERT INTO student VALUES(i,CONCAT('李四',i),'女',22+i,IF(MOD(i,5)=0,5,MOD(i,5)));
     END IF;
     SET i=i+1;
   END WHILE;
 END $$
 
 CALL proc()
 
 SELECT * FROM student
 
 SELECT * FROM class
 
 -- 条件链接查询
 SELECT student.stuId,student.stuName,student.stuSex,student.stuAge
        ,class.className FROM student , class 
        WHERE student.cid=class.classId
        
 -- 别名
 SELECT S.stuId,S.stuName,S.stuSex,S.stuAge
        ,C.className FROM student S, class C
        WHERE S.cid=C.classId ORDER BY S.stuId
        
 -- 级连(删除 更新 查询)
 
 -- 内连接 外链接 交叉链接
 -- 内连接取两张数据表的交集
 SELECT S.stuId,S.stuName,S.stuSex,S.stuAge
        ,C.className FROM student S INNER JOIN class C
        ON S.cid=C.classId ORDER BY S.stuId
        
        
 INSERT INTO class VALUES(6,'1000006')
 
 INSERT INTO student VALUES(51,'张无忌','男',22,NULL)
 
 SELECT * FROM class
 
 SELECT * FROM student
 
 -- 外链接(左外链接，右外链接)
 -- left join 为分界点(left 左边为主表 主表中存在的数据全部出现)
 SELECT S.stuId,S.stuName,S.stuSex,S.stuAge
        ,C.className FROM student S LEFT JOIN class C
        ON S.cid=C.classId ORDER BY S.stuId
  --  RIGHT JOIN  右边的数据表为主表 主表的数据全部出现 没有对应的记录为null  
  SELECT S.stuId,S.stuName,S.stuSex,S.stuAge
        ,C.className FROM student S RIGHT JOIN class C
        ON S.cid=C.classId ORDER BY S.stuId
        
  -- 交叉链接(笛卡尔积)
  
  SELECT S.stuId,S.stuName,S.stuSex,S.stuAge
        ,C.className FROM student S CROSS JOIN class C
   
 
 
 -- 分页(51) 
   SELECT * FROM student WHERE stuId BETWEEN 1 AND 10
   
   DELETE FROM student WHERE stuId IN(1,4,8)
-- 性能比较差
   SELECT * FROM student LIMIT 20,10   
   
 -- 子查询(嵌套查询)
   SHOW TABLES

   SELECT * FROM student   
 
 -- 查询班级编号为1 所有的学员信息
   
   SELECT * FROM student WHERE cid=1
   
 -- 查询班级名称为1000001 所有的学员信息
   -- 关系是引用的是id 1000001 对应的 id  student 查询数据
   -- SQL () 优先执行(= > < )
   SELECT * FROM student WHERE cid=
	(SELECT classId FROM class WHERE className='100001')
   
   -- >any(1,2,3,54,56) 大于 any(值) 值的最小值
   -- >all(1,2,3,4,5,6) 大于 all(值) 值的最大值
   
   SELECT * FROM student WHERE stuId >ALL(SELECT stuID FROM student LIMIT 0,10)

   DELETE FROM student WHERE cid=(SELECT classId FROM class WHERE className='1000001')   
   
   UPDATE student SET stuAge=stuAGe+1 WHERE cid=
	(SELECT classId FROM class WHERE className='1000001')
   
   INSERT INTO student1 SELECT * FROM student WHERE stuId=10
   
   CREATE TABLE student2 AS SELECT * FROM student
   
   DROP TABLE IF EXISTS student2
   
   -- exists()  File
   -- exists(select...) 如果子查询能查询结果(能返回结果 不管结果多少) true
   -- 如果西查询不能查询结果(没有任何的结果返回) false
   SELECT * FROM student WHERE EXISTS(SELECT * FROM class WHERE classId=100)   
   
   SELECT * FROM student WHERE TRUE/FALSE
  
   
 -- 视图
  -- 视图是内存表(不存在在物理磁盘数据文件和结构文件) 存储在DBMS内部
  -- 视图的数据都是来源于真是物理表 merge(存储引擎) 表是结合了多张MYISAM存储引擎的表 Memory(数据全部在内存 DBMS)
  -- 因为存储在DBMS中(存在内存 性能高于物理表(存在磁盘)) 
  -- 视图映射物理表的数据(10列 系统 划分 角色 不同的角色对于当前数据敏感度不一样) 
  -- 不同的角色创建基于当前物理表的视图对应
  
  -- 创建
    
    CREATE VIEW view_name AS SELECT .....
    
    -- 视图的结构是根据指向的查询语句的结果集的结构一致
    -- 保存的结果集
    CREATE VIEW student_view AS SELECT stuId,stuName FROM student
    
    SELECT * FROM student_view

-- 视图对于用户角度来说 视图基本和真实一致  
-- 视图上的操作都会反映到视图映射的物理表(增删改查)
  
    INSERT INTO student_view VALUES(52,'张无忌')

    DELETE FROM student_view WHERE stuId=52    
    
    UPDATE student_view SET stuName='张翠山' WHERE stuId=51
    
    SELECT * FROM student_view WHERE stuId=51
    SELECT * FROM student WHERE stuId=51
    
    DROP VIEW IF EXISTS student_view
    
    CREATE VIEW student_view AS SELECT * FROM student
    
    SELECT * FROM class
    -- 增加视图数据的时候需要遵循视图映射的物理表的所有的约束
    INSERT INTO student_view VALUES(52,'张三丰','男',66,5)
    
    SELECT * FROM student_view
    
    DELETE FROM student WHERE stuId<10
    
    -- 视图如果指向的是一个多表查询(内连接 外链接)
    
    CREATE VIEW student_class_view
    AS
     SELECT S.stuId,S.stuName,S.stuSex,S.stuAge,C.className 
       FROM student S INNER JOIN Class C 
       ON S.cid=C.classId
 
    SELECT * FROM student_class_view
    
    DELETE FROM student_class_view WHERE stuId=52

    -- SQL 不区分大小写(SQL标准是大写)
   
    -- 更新成功(没有涉及到关联数据)
    UPDATE student_class_view SET stuAge=stuAGe+10 WHERE stuId=52
  
 
    INSERT INTO student_class_view VALUES(53,'张翠山','男',66,'100002')
    
 
    -- 视图 表 数据库 约束 数据对象   DDL语句 create  drop 
    
    -- 索引(*****) 
    -- 作用:提高查询数据的速度(性能) 书的目录一样 快速的定位到数据
    -- 表-->10万 行数据 速度
    -- 索引存在磁盘文件 IO操作 存储索引的空间 成为索引页 存储数据的空间称为数据页
    -- 不足(少量的数据 排序 )
    -- mysql 索引分为两种 (hash索引 b+tree索引) hash索引速度远远快于b+tree 
    -- hash 排序问题 等额 不能作为范围索引 = in  merge 引擎支持  INNODB b+tree
    -- 索引:索引页和数据页存储分为 两种:聚集索引(聚簇索引) 非聚集索引(非聚簇索引)
    -- 聚集索引:索引页排序和数据页排序保持一致 不一致为非聚集索引
    -- 一张数据表只能创建一个聚集索引 可以创建多个非聚集索引
    -- 主键默认就是一个聚集索引(如果一张数据表存在主键，那么就不能再创建聚集索引)
    
    -- 按照主键查找 
   
   -- 索引创建 1:普通索引 2:组合索引 3:唯一索引(唯一性约束) 4:主键索引 5:全文索引(INNODB 不支持)
   -- 创建表的时候创建
   
   SHOW TABLES
   -- 普通索引
   CREATE TABLE student(
       stuId INT AUTO_INCREMENT,
       stuName VARCHAR(20) NOT NULL,
       stuSex ENUM('男','女')   ,
       stuAge INT,
       PRIMARY KEY(stuId),-- 主键索引(聚簇索引)
       KEY(stuId) -- 索引(普通)
   )
   
   -- 查看student数据表的所有的索引
   SHOW INDEX FROM student
   
   SELECT * FROM information_schema.tables WHERE table_name='Student'
   
   DROP TABLE student
   
   
   
  
   -- 查看当前student数据表的所有索引 
   SHOW INDEX FROM student
   
   -- 创建索引(创建表之后)
   CREATE INDEX INDEX_STUID ON student(stuId)
   
   ALTER TABLE student ADD INDEX INDEX_STUID(stuId)
	
   -- 删除索引
   ALTER TABLE student DROP INDEX Index_STUID

   -- 列出来可以使用有两个 有限使用主键索引
   EXPLAIN SELECT * FROM student  WHERE stuId=1 

   -- 建立索引过滤的条件需要过滤是索引列   
   -- 索引失效
   -- not in 不使用   
   -- is null 不能过滤为null
   -- or not 
   -- 索引列参与运算
   EXPLAIN SELECT * FROM student WHERE (stuId+1)=10

   SELECT * FROM student

   DELETE FROM student WHERE stuId=1

   SELECT * FROM student WHERE stuId>=1 LIMIT 10,10

   10万

   EXPLAIN SELECT * FROM student WHERE stuId>=1 LIMIT 20,10   
   
   DELETE FROM student WHERE stuId IN (5,8,12,16,23,26)

   EXPLAIN SELECT stuId FROM student LIMIT 10,1

   EXPLAIN SELECT * FROM student WHERE stuId>=
	(SELECT stuId FROM student LIMIT 0,1) LIMIT 10  
   
   EXPLAIN SELECT * FROM student LIMIT 10,10
   
   -- student表有两个索引 两个索引都是建立在stuId列上 过滤的条件没有使用stuId
   EXPLAIN SELECT * FROM student WHERE stuName='张三2'
   
   SHOW CREATE PROCEDURE proc
   
   -- 组合索引(多列合并为一个索引)
   CREATE INDEX INDEX_STUID_STUNAME ON student(stuName,stuid)

   ALTER TABLE student ADD INDEX INDEX_STUID(stuId)   
   
   SHOW INDEX FROM student

   EXPLAIN SELECT * FROM student WHERE stuId=2
   
   EXPLAIN SELECT * FROM student WHERE stuId=2 AND stuName='张三2'
   -- 组合索引从做向右匹配
   -- 两个索引 主键(stuId) 组合索引 stuName stuId,stuName
   EXPLAIN SELECT * FROM student WHERE stuName='张三2' OR stuId=2
   
   -- 唯一索引

   SHOW INDEX FROM student
   
   ALTER TABLE student DROP INDEX index_stuId_STUNAME

   -- 创建1:在创建表的时候,创建一个唯一约束就默认是唯一索引

   DROP TABLE student   
   -- 1:
   CREATE TABLE student(
     stuid INT AUTO_INCREMENT,
     stuName VARCHAR(20) UNIQUE NOT NULL,
     stuSex ENUM('男','女'),
     stuAge INT,
     PRIMARY KEY(stuId)
   )   
   
   -- 2:
   CREATE TABLE student(
     stuid INT AUTO_INCREMENT,
     stuName VARCHAR(20) NOT NULL,
     stuSex ENUM('男','女'),
     stuAge INT,
     PRIMARY KEY(stuId),
     UNIQUE KEY(stuName)
   )
   
   -- 3:
   CREATE TABLE student(
     stuid INT AUTO_INCREMENT,
     stuName VARCHAR(20) NOT NULL,
     stuSex ENUM('男','女'),
     stuAge INT,
     PRIMARY KEY(stuId)
   )   
   
   ALTER TABLE student ADD UNIQUE INDEX INDEX_UQ_STUNAME(stuName)

   ALTER TABLE student DROP INDEX INDEX_UQ_STUNAME
   
   -- 4:
   CREATE UNIQUE INDEX INDEX_UQ_STUNAME ON student(stuName)

   CALL proc()

   -- 索引 % _ 通配符

   EXPLAIN SELECT * FROM student WHERE stuName LIKE '张三2'

   SHOW INDEX FROM student

   ALTER TABLE student DROP INDEX INDEX_STUNAME   
   
   -- 主键
   
   -- N个网页 定义关键字  --> 搜索 关键字匹配
    
   -- 全文索引(MYISAM 支持) 
   -- 全文索引(大文本的情况)
   -- 全文索引只能搜索英文 
   CREATE INDEX INDEX_STUNAME ON student(stuName)
   CREATE INDEX INDEX_STUNAME_STUID ON student(stuName,stuId)
   CREATE UNIQUE INDEX INDEX_STUNAME ON student(stuName)
   CREATE FULLTEXT INDEX INDEX_STUNAME ON student(stuName)
   
   ALTER TABLE student ADD INDEX INDEX_STUID(stuId)
   ALTER TABLE student ADD INDEX INDEX_STUID_STUNAME(stuName,stuId)
   ALTER TABLE student ADD UNIQUE INDEX INDEX_STUNAME(stuName)
   ALTER TABLE student ADD PRIMARY KEY(stuId)
   ALTER TABLE student ADD FULLTEXT INDEX INDEX_STUNAME(stuName)
   
   
   -- 索引 数据库优化的必备条件
   
   
   -- 函数(作用:类似于Java中定义的方法)
   -- 1:存储在服务器 1-1:安全性 隐藏实现细节以及敏感的数据(Java方法 外部只需要调用 不关心或者不知道内部实现)
                 --  1-2:优化 服务器在编译的时候会优化
   -- 2:必须返回值

   -- SQL语句中的分号表示的是当前SQL语句开始执行(结束的符号)
 
   SELECT * FROM student;
   
      -- 语法:
      CREATE FUNCTION function_name RETURNS 类型(返回值的类型)
      BEGIN
        function_body
      END
      
      
    -- SQL中变量
    -- SQL变量分为两种(1:全局变量 2:局部变量)    
    -- 全局变量不需要定义
    -- 在变量名前面加上@符号 标识全局变量

    -- 局部变量(需要自定义)    
    -- 局部变量必须定义在begin end 之间(begin 类似于Java{  end 类似于Java})
    -- 不支持匿名SQL块(不能直接定义begin end)
    -- begin end 必须定义在对象中(函数 存储过程 触发器)
    
    DROP FUNCTION IF EXISTS fun;
    DELIMITER $$
    CREATE FUNCTION fun() RETURNS INT
    BEGIN
      -- 给变量赋值
      SET @i=20;
      -- 返回变量的值
      RETURN @i;
    END $$
    
    
    DROP FUNCTION IF EXISTS fun
    
    DELIMITER $$
    CREATE FUNCTION fun() RETURNS INT
    BEGIN
      -- declare 定义变量的关键字 
      -- declare i(变量的名称) 数据类型
      DECLARE i INT;
      SET i=10;
      RETURN i;
    END
    
    
    DELIMITER $$
    CREATE FUNCTION fun() RETURNS INT
    BEGIN
      -- declare 定义变量的关键字 
      -- declare i(变量的名称) 数据类型
      -- declare i int default 100 定义变量并初始化变量的值
      DECLARE i INT DEFAULT 100;
      RETURN i;
    END
    
    
    
    DELIMITER $$
    CREATE FUNCTION fun() RETURNS INT
    BEGIN
     DECLARE i INT DEFAULT 1;
     -- 给变量赋值(赋常量值)
     SET i=100;
     RETURN i;
    END $$
    

 DROP FUNCTION IF EXISTS fun    
    
     DELIMITER $$
    CREATE FUNCTION fun() RETURNS INT
    BEGIN
     DECLARE i INT;
     SELECT stuId INTO i FROM student WHERE stuName='张三2';
     RETURN i;
    END $$
    

   CREATE FUNCTION fun() RETURNS INT
    BEGIN
     DECLARE i INT;
     -- 使用select 给变量赋值 (查询数据表中某一个数据赋值给变量)
     SELECT stuId INTO i FROM student WHERE stuName='张三2';
     RETURN i;
    END $$   
    
     
     
     DELIMITER $$
     CREATE FUNCTION fun(sname VARCHAR(20)) RETURNS INT
     BEGIN
      DECLARE sid INT;
      SELECT stuId INTO sid FROM student WHERE stuName=sname;
      RETURN sid;
     END $$
     
     
     DROP FUNCTION IF EXISTS fun
     DELIMITER $$
     CREATE FUNCTION fun(i INT,age INT) RETURNS VARCHAR(20)
     BEGIN
      DECLARE sname VARCHAR(20);
      SELECT stuName INTO sname FROM student WHERE stuId=i AND stuAge=age;
      RETURN sname;
     END $$
     
     
     -- 变量(定义 赋值)
     
     -- 流程控制(if case while repeat loop)
     
     -- if 判断(程序判断)     
     
     DROP FUNCTION IF EXISTS fun
     DELIMITER $$
     CREATE FUNCTION fun(i INT) RETURNS VARCHAR(20)
     BEGIN
       DECLARE str VARCHAR(20);
       IF i>10 THEN
         SET str='大于';
       ELSEIF i<10 THEN
         SET str='等于';
       ELSE
         SET str='小于';
       END IF;
       RETURN str;
     END $$
     
     DELIMITER $$
CREATE FUNCTION fun(i INT) RETURNS VARCHAR(20)
BEGIN
  DECLARE str VARCHAR(20);
  IF i>10 THEN
    SET str='大于';
  ELSEIF i<10 THEN
    SET str='小于';
  ELSE
    SET str='等于';
  END IF;
  RETURN str;
 END $$
 
 
 DELIMITER $$
CREATE FUNCTION fun(i INT) RETURNS VARCHAR(20)
BEGIN
  DECLARE str VARCHAR(20);
  IF i>10 THEN
    SET str='大于';
  END IF;
  RETURN str;
 END $$
 
 DELIMITER $$
CREATE FUNCTION fun(i INT) RETURNS VARCHAR(20)
BEGIN
  DECLARE str VARCHAR(20);
  IF i>10 THEN
    SET str='大于';
  ELSE
    SET str='小于或等于';
  END IF;
  RETURN str;
 END $$
 
 
 -- case  (Java switch)
 
 DROP FUNCTION fun
 DELIMITER $$
CREATE FUNCTION fun(w INT) RETURNS VARCHAR(20)
BEGIN
  DECLARE str VARCHAR(20);
  CASE w
  WHEN 1 THEN SET str='星期一';
  WHEN 2 THEN SET str='星期二';
  WHEN 3 THEN SET str='星期三';
  WHEN 4 THEN SET str='星期四';
  WHEN 5 THEN SET str='星期五';
  WHEN 6 THEN SET str='星期六';
  WHEN 7 THEN SET str='星期天';
  END CASE;
  RETURN str;
 END $$
       

 -- case (做范围判断)   
     
     DROP FUNCTION fun
 DELIMITER $$
CREATE FUNCTION fun(score INT) RETURNS VARCHAR(20)
BEGIN
  DECLARE str VARCHAR(20);
  CASE 
  WHEN score>90 THEN SET str='优秀';
  WHEN score>80 THEN SET str='良好';
  WHEN score>70 THEN SET str='还行';
  WHEN score>=60 THEN SET str='及格';
  WHEN score<60 THEN SET str='不及格';
  END CASE;
  RETURN str;
 END $$
  

  -- 循环
   DROP FUNCTION fun
 DELIMITER $$
CREATE FUNCTION fun(num INT) RETURNS INT
BEGIN
  DECLARE result INT DEFAULT 0;
  DECLARE i INT DEFAULT 1;
  WHILE i<=num DO
    SET result=result+i;
    SET i=i+1;
  END WHILE;
  RETURN result;
 END $$


 -- loop
  DELIMITER $$
CREATE FUNCTION fun(num INT) RETURNS INT
BEGIN
  DECLARE result INT DEFAULT 0;
  DECLARE i INT DEFAULT 1;
  haha:LOOP
    IF i>num THEN
      LEAVE haha;
    END IF;
    SET result=result+i;
    SET i=i+1;
  END LOOP;
  RETURN result;
 END $$

   SELECT fun(20)    
    
    
    DESC student
    SELECT * FROM student WHERE stuId=10
  

SHOW CREATE FUNCTION fun

-- select 1:查询 2:赋值 3:输出    
    
    
    CREATE DEFINER=`root`@`localhost` FUNCTION `fun`(num INT) RETURNS INT(11)
BEGIN
  DECLARE result INT DEFAULT 0;
  DECLARE i INT DEFAULT 1;
  haha:LOOP
    IF i>num THEN
      LEAVE haha;
    END IF;
    SET result=result+i;
    SET i=i+1;
  END LOOP;
  RETURN result;
 END
 

DROP FUNCTION fun 
 
 -- repeat
DELIMITER $$
CREATE FUNCTION fun(num INT) RETURNS INT(11)
BEGIN
  DECLARE result INT DEFAULT 0;
  DECLARE i INT DEFAULT 1;
  REPEAT
    SET result=result+i;
    SET i=i+1;
    UNTIL i>num
    END REPEAT;
  RETURN result;
 END $$
 
 
 -- 变量(局部 全局 ) if if else  if elseif else  case(两种) 循环(三种)
 
    
    SELECT fun(100)
    
    
    SHOW TABLES
    
    CREATE TABLE card(
       cardId INT AUTO_INCREMENT,
       cardNumber VARCHAR(18),   
       PRIMARY KEY(cardId) 
    )
    
    CREATE TABLE person(
        personId INT AUTO_INCREMENT,
        personName VARCHAR(20),
        cardId INT,
        PRIMARY KEY(personId),
        CONSTRAINT FK_CARDID FOREIGN KEY(cardId) REFERENCES card(cardId)    
    )
    
    ALTER TABLE person ADD CONSTRAINT UQ_CARDID UNIQUE(cardId)
    
    INSERT INTO card VALUES(DEFAULT,'100010001000100011')
    INSERT INTO person VALUES(DEFAULT,'张三',2)
    
    DELETE FROM person    
    
    SELECT * FROM person
    
    
    DROP TABLE card
    DROP TABLE person
    
    
    
    CREATE TABLE card(
       cardId INT AUTO_INCREMENT,
       cardNumber VARCHAR(18),
       PRIMARY KEY(cardId)    
    )
    
    CREATE TABLE person(
       personId INT,
       personName VARCHAR(20),
       PRIMARY KEY(personId),
       CONSTRAINT FK_PERSONID FOREIGN KEY(personId) REFERENCES card(cardId)
    )
    
    INSERT INTO card VALUES(2,'100010001000100011')
    
    SELECT * FROM card
    
    INSERT INTO person VALUES(1,'张三')
    INSERT INTO person VALUES(2,'李四')
   
    SELECT * FROM person
    
    SELECT P.personId,P.personName,C.cardId,C.cardNumber FROM person P INNER JOIN card C ON P.personId=C.cardId
    
    SELECT personId,personName,cardId,cardNumber FROM person INNER JOIN
	card ON Person.personId=Card.cardId WHERE personId=1
      
      

USE test

SHOW TABLES

DROP TABLE student

CREATE TABLE class(
   classId INT AUTO_INCREMENT,
   className VARCHAR(20),
   PRIMARY KEY(classId)
)

CREATE TABLE student(
   stuId INT AUTO_INCREMENT,
   stuName VARCHAR(20),
   stuSex ENUM('男','女'),
   stuAge INT,
   cid INT,
   PRIMARY KEY(stuId),
   CONSTRAINT FK_CID FOREIGN KEY(cid) REFERENCES class(classId)
)

SELECT * FROM class
SELECT * FROM student

INSERT INTO class VALUES(DEFAULT,'100002')

INSERT INTO student VALUES(DEFAULT,'殷素素','女',22,2)
INSERT INTO student VALUES(DEFAULT,'赵敏','女',21,2)


SELECT S.stuId,S.stuName,S.stuSex,S.stuAge,C.classId,C.className
  FROM student S INNER JOIN class C 
  ON S.cid=C.classId
  

SELECT S.stuId,S.stuName,S.stuSex,S.stuAge,C.classId,C.className
  FROM student S RIGHT JOIN class C 
  ON S.cid=C.classId
  
SELECT * FROM class

SELECT * FROM student WHERE cid=1
-- person Id name cid(外键 添加唯一性约束)
-- card id number 
-- person Id(主键 同时 外键) name
-- card id number
-- 一对一 (主外键描述(第一种有明确的外键))

-- student id name sex age cid(外键)
-- classtable id name
-- 一对多(主外键描述)
-- 外键定义在多方

-- product id  name
-- order  id name
-- product_order_table pid(外键引用product Id) oid(外键 引用order Id)(联合主键)
-- 多对多(商品  订单)


SHOW TABLES

CREATE TABLE users(
    userId INT AUTO_INCREMENT,
    userName VARCHAR(20),
    userPwd VARCHAR(100),
    PRIMARY KEY(userId)
)



SELECT * FROM users

UPDATE users SET userPwd='a111' WHERE userId=1


INSERT INTO users VALUES(DEFAULT,#{userName},#{userPwd}),()()()


CREATE TABLE book(
  bookId INT AUTO_INCREMENT,
  bookName VARCHAR(20),
  bookAuthor VARCHAR(20),
  bookPrice DECIMAL(6,2),
  bookInfo TEXT,
  PRIMARY KEY(bookId)
)

DROP PROCEDURE proc
DELIMITER $$
CREATE PROCEDURE proc()
BEGIN
 DECLARE i INT DEFAULT 1;
 WHILE i<=50 DO
   INSERT INTO book VALUES(DEFAULT,CONCAT('Java权威指南',i),CONCAT('张三',i),44.55+i,'测试数据');
   SET i=i+1;
 END WHILE;
END $$


CALL proc()

SELECT * FROM book








-- 切换数据库
USE test

-- 查询当前数据库的所有的数据表
SHOW TABLES

-- SQL标准的是大写(SQL大小写不区分)
SELECT * FROM book

-- 创建数据库
CREATE DATABASE demo
-- 查询当前服务器上所有的数据库
SHOW DATABASES
--
USE demo
-- 当前数据库下创建数据表
CREATE TABLE student(
   stuId INT AUTO_INCREMENT,
   stuName VARCHAR(20),
   stuSex ENUM('男','女'),
   stuAge INT,
   PRIMARY KEY(stuId)
)
-- 删除当前的数据库
DROP DATABASE demo

-- 数据表
CREATE TABLE table_name(列的名称 类型 属性,)

-- mysql 数据类型
-- 整型
TINYINT(m) 1字节 -128 127 m 占用的宽度(实际的取值范围没有关系)
SMALLINT(m) 2字节 -32768 32767
MEDIUMINT(m) 3字节
INT(m) 4字节 
BIGINT(m) 8字节

-- 浮点型
FLOAT(m,d) 4字节 (m总长度 d小数位)
DOUBLE(m,d) 8字节
DECIMAL(m,d) m<65 d<30

-- 字符型
CHAR(30) 固定长度的字符 (3)字符数
VARCHAR(20) 变长字符 (20)字符数 
NCHAR(3) 固定长度的字符
NVARCHAR(20) 变长的字符
TINYTEXT 变长字符文本
TEXT 
MEDIUMTEXT 
LONGTEXT 

-- 二进制数据
BLOB 

-- 日期型
DATE 年月日(2017-10-22)
TIME 时分秒
DATETIME 年月日时分秒
TIMESTAMP 年月日时分秒

-- 枚举
ENUM 
-- 添加的值只能是enum定义过的值 只能取一个
-- 添加的值只能是set定义过的值 同时取多个
SET 类型


USE test

SHOW TABLES

DROP TABLE student

CREATE TABLE student(
   stuId INT AUTO_INCREMENT,
   stuSex SET('男','女'),
   PRIMARY KEY(stuId)
)

DROP TABLE student

INSERT INTO student VALUES(DEFAULT,'男')

SELECT * FROM student
  
-- 创建表  建模

-- 类()

-- 主键(当前数据的唯一标识)  
-- 主键原则()
  
CREATE TABLE student(
  stuId INT,
  stuName VARCHAR(20),
  stuSex ENUM('男','女'),
  stuAge INT,
  -- 主键列
  PRIMARY KEY(stuId)
)

-- 主键约束(值不能为null 只能不能重复)

-- 查询当前服务器上所有的数据库
SHOW DATABASES

-- mysql 服务器实例(一个物理计算机上可以安装多个mysql实例(服务器))
-- 一个标准的mysql实例 (一套DBMS 和 完整数据文件)

-- 一个mysql服务实例可以存在多个数据库(逻辑数据库 一个独立的存储单元)

-- 创建数据库
CREATE DATABASE test
-- 使用创建的数据库(切换当前连接的数据) 当前连接的数据库,只能查询到当前数据库的数据
USE test

USE information_schema

-- 查询当前数据库下的所有的数据表
SHOW TABLES

-- 数据表(存储数据  服务器 --> 数据库 --> 数据表)
-- 数据表(二维表的形式存储数据) 定义当前表的结构(列)
CREATE TABLE student(
  stuId INT,
  stuName VARCHAR(20),
  stuSex ENUM('男','女'),
  stuAge INT
)

DROP TABLE student

CREATE TABLE test(
   tid INT,
   tt FLOAT(4,2)
)
SELECT * FROM test
INSERT INTO test VALUES(1,34.56789)
DROP TABLE test
-- 插入数据
INSERT INTO student VALUES(2,'张三','男',22)
-- 查询数据
SELECT * FROM student

UPDATE student SET stuName='李四' WHERE stuId=1
UPDATE student SET stuName='王五',stuSex='女' WHERE stuId=1

UPDATE student SET stuName='张无忌'

-- 当前数据表的中数据全部删除
DELETE FROM student 
-- 只删除stuId=2 的数据
DELETE FROM student WHERE stuId=2


-- 整型
TINYINT 1字节 -128 127
SMALLINT 2字节 -32768 32767
MEDIUMINT 3字节 
INT 4字节
BIGINT 8字节

INT(10) (10)标识查询的时候占用的宽度(取值范围没有关系)

-- 浮点型
FLOAT(m,d) 4字节 m 当前总长度 d 小数位的长度
DOUBLE(m,d) 8字节
DECIMAL(m,d) m<64  d<30

-- 字符型
CHAR(4) 固定长度的字符型 (4) 长度
VARCHAR(20) 变长的字符型 (20) 长度
NCHAR(4) 固定长度的unicode字符
NVARCHAR(20) 变长的unicode字符
TEXT 文本
MEDIUMTEXT 中等长度的文本
LONGTEXT 大文本

-- 二进制
BLOB 二进制数据

-- 日期型
DATE 年月日
TIME 时分秒
DATETIME 年月日时分秒
TIMESTAMP 带有时间戳的日期

-- 枚举型
ENUM

-- set
SET

ENUM('男','女')    '男'    '女'

SET('男','女')  '男'   '女'   '男,女'





















  