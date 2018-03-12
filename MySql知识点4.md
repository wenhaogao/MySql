-- 函数

 -- 自定义函数(数据对象) 存储服务器
 
 -- MYSQL 函数必须有返回值 returns 数据类型
 
```
DELIMITER $$
  CREATE FUNCTION fun(i INT) RETURNS INT
  BEGIN
     DECLARE result INT DEFAULT 0;
     DECLARE n INT DEFAULT 1;
     WHILE n<=i DO
        SET result=result+n;
        SET n=n+1;
     END WHILE;
     RETURN result;
  END $$
 
SELECT fun(10)  
  

DROP FUNCTION IF EXISTS fun
```


 -- 触发器(数据对象 存储在服务器)
 
 -- 和函数和存储过程的区别:自动执行  函数和存储需要调用(监听器 监听一个对象的某个操作)
 
 -- 构造方法(当创建当前类的对象时自动调用)


```
DELIMITER $$
```

 -- 创建触发器 触发器的名称为mytrigger
 CREATE TRIGGER mytrigger 
 
 -- after 在监听的对象上操作之后执行触发器  before
 在监听的多上的操作之前执行触发器
 
 -- delete 监听的操作时删除  insert   update 
 
 -- on student 监听的对象
 
```
AFTER DELETE ON student
```

 
 -- for each row 当前的触发器(操作影响多少行数据那么就执行多少次触发器 行级触发器)
 
 -- 没有for each row 当前的删除不管删除了多少行数据 只执行一次 语句级触发器
 
 -- mysql 不支持语句级触发器(只支持行级触发器) 
 FOR EACH ROW
 
 -- 触发器内容的开始
 
```
BEGIN
      
 END $$
```

 
 -- 在触发器执行期间 系统会自动根据当前触发器监听的对象来创建两个数据对象
 
 -- old  new (结构就和触发器监听的对象保持一致)
 
 -- old 用来保存当前删除的数据  和 更新前的原始数据
 
 -- new 用来保存当前增加的数据  和 更新后的新数据
 
 
```
CREATE TABLE student1 AS SELECT * FROM student WHERE 1=2
 
 
 SELECT * FROM student
 
 DELETE FROM student
 
 
 
DELIMITER $$
CREATE TRIGGER mytrigger 
AFTER DELETE ON student
FOR EACH ROW
BEGIN
  INSERT INTO student1 VALUES(old.stuId,old.stuName,old.stuSex,old.stuAge,old.classId);
END $$ 

DELIMITER $$
CREATE TRIGGER mytrigger1
BEFORE INSERT ON student
FOR EACH ROW
BEGIN
  INSERT INTO student1 VALUES(new.stuId,new.stuName,new.stuSex,new.stuAge,new.classId);
END $$

INSERT INTO student VALUES(10,'王五','男',22,2)


SELECT * FROM student1

SELECT * FROM student

DELETE FROM student WHERE stuId=10
```

 -- 事务
 
 -- 事务数据库中一个最小的执行单元
 
 -- 事务是一种机制
 
 -- 数据库中自动事务 还是手动事务
 
 -- 数据库在执行命令之前自动开始一个事务 
 -- 事务执行完毕后
 没有错误的情况下自动提交 出现错误自动回滚
 
 -- 手动事务 需要手动开始事务
#####  -- 事务的四个特性:
 
 -- 1:原子性(一个事务是一个整体 不可分割)
 
 -- 2:一致性(一个事务只会产生一个结果 要么都成功要么都失败)
 
 -- 3:永久性(事务产生的结果永久保存 影响数据文件)
 
 -- 4:隔离性(事务和事务之间 相互隔离的 执行像互不干扰)
 
 -- 转账
 
 -- update -1000    update +1000  默认情况下 自动事务 为每一条SQL命令开启一个事务
 
 
 -- start transaction
 
```
START TRANSACTION
    DELETE FROM student WHERE stuId=1
    DELETE FROM student WHERE stuId=3
    
    
SELECT * FROM student

ROLLBACK

COMMIT
```

-- 事务的隔离性

-- 事务的隔离级别

-- 当前事务的隔离级别
SELECT @@tx_ISOLATION

##### -- 四种事务隔离级别
-- 读未提交数据  read uncommitted  造成脏读

-- 读已提交数据  read committed    幻读(在一个事务中多次查询的结果是不一致)

-- 可重复度      repeatable-read   保障同一个事务中多次查询的结果保持一致

-- 串行化        SERIALIZABLE

-- mysql 默认的隔离级别未 可重复读



 -- 设置当前会话的事务的隔离级别未读未提交数据
 
```
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED
 SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED
 
 SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ
 
 SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE
 
 START TRANSACTION
 UPDATE student SET stuName='张三' WHERE stuId=1

 ROLLBACK 
 COMMIT
 SELECT * FROM student
 
 DELETE FROM student WHERE stuId=1
 
 COMMIT
 
 INSERT INTO student VALUES(11,'hello','男',22,1)
 
 COMMIT
 
 INSERT INTO student VALUES(12,'张翠山','男',22,2)
 
 SELECT * FROM student
 
 
 
 START TRANSACTION
 SELECT * FROM student
```
