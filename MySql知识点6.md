#### -- 索引 

##### -- 聚集索引
(索引的排序和数据的排序保持一致) 字典(按照拼音查找)  

-- a - z   a - z 排序

-- 索引的排序还是数据的排序 都是一致  聚集索引  聚簇索引

##### -- 非聚集索引 
索引的排序和数据的排序不一致  字典(按照偏旁部首查找) 

##### -- 主键索引 
就是一个典型聚集索引

##### -- 聚集索引 
一张表只能存在一个(排序规则) 一张表如果存在主键索引 那么就不能再添加聚集索引

-- 添加的索引默认都是非聚集索引

-- 一张数据表的非聚集索引可以存在多个

-- 控制流程

-- 变量 (MYSQL 变量存在两种)

-- 局部变量(只能定义在begin end 之间)

-- begin end 定义SQL块({begin }end)

-- MYSQL 不支持SQL块

-- 如果需要定义begin end

需要定义数据对象

-- 存储过程

-- 存储过程:

-- 类似于程序语言中的函数 或者 方法

-- 先要创建  调用

-- 创建 存储服务器 提前编译

-- 创建了一个存储过程 

在服务器上保存一个数据对象   创建的的数据对象 需要编译(DBMS) 

-- 存储过程优点:

-- 1:效率高(提前编译,提前对存储过程进行优化)

-- 2:安全性高(封装性，隐藏敏感的数据和业务 不暴露给客户端)

-- 3:减少客户端和服务器之间网络流量

-- 4:使SQL具有模块化功能

-- 存储过程的创建

-- 创建 procedure(过程) proc() 存储过程的名字

```
CREATE PROCEDURE proc()
-- begin{
BEGIN
END
-- }
```

-- 修改当前的SQL结束符号

```
DELIMITER $$
CREATE PROCEDURE proc()
BEGIN
```

   -- select 查询某张数据表中的数据  
```
select 'hello';
```
 输出(以表格的形式输出)

```
SELECT 'hello';
END $$
```


-- 调用存储过程

```
CALL proc()

DROP PROCEDURE IF EXISTS proc

DELIMITER $$
CREATE PROCEDURE proc()
BEGIN
```

-- MYSQL中定义变量必须使用declare 关键字

-- 先定义变量的名称  在规定数据类型

 
```
DECLARE i INT;
```

 -- MYSQL 规定
 
 给变量赋值必须使用set关键字

```
SET i=10;
```

 
 -- 以表格的形式输出变量的值

```
SELECT i;
END $$
```


-- select 给变量进行赋值

-- 变量的赋值
-- 1: set 只能给当前的变量赋常量值(10,200)

-- 2: select 可以把数据表的列的数据查询出来赋值给变量



```
DELIMITER $$
CREATE PROCEDURE proc()
BEGIN
  DECLARE i INT;
  SELECT stuId INTO i FROM student WHERE stuName='张三3';
  SELECT i;
END $$


DROP PROCEDURE IF EXISTS proc

DELIMITER $$
CREATE PROCEDURE proc()
BEGIN
```

  -- 定义变量并给变量赋初始值
 
```
DECLARE i INT DEFAULT 1;
  SELECT i;
  SET i=100;
  SELECT i;
END $$

-- 存储过程 类似于方法 (参数)

DELIMITER $$
CREATE PROCEDURE proc(i INT)
BEGIN
   DECLARE sname VARCHAR(20);
   SELECT stuName INTO sname FROM student WHERE stuId=i;
   SELECT sname;
END $$
```





CALL proc(4)



SELECT * FROM student WHERE stuName='赵敏'


DELIMITER $$
CREATE PROCEDURE proc(i INT , sname VARCHAR(20))
BEGIN
   SELECT * FROM student WHERE stuId=i AND stuName=sname;
END $$

-- 返回值
-- 在存储过程的参数前添加 out 那么表示当前参数是一个输出参数
DELIMITER $$
CREATE PROCEDURE proc(i INT , OUT sname VARCHAR(20))
BEGIN
-- sname 赋值(查询进行赋值)
  SELECT stuName INTO sname FROM student WHERE stuId=i;
END $$


-- 调用存储过程 (输入参数 3 , 输出参数 变量)
-- @sname 如果在变量的前面添加@ 当前变量就是一个全局变量
-- 局部变量的作用域就在当前的begin end之间
-- 全局变量作用域就是当前的会话(只要当前的会话没有断开 全局变量都能使用)
-- 全局变量不需要提前定义


-- 会话(客户端和服务器一次连接)(打电话 拨号 对方接听 双方就是建立一个会话 说的每句话相当于发送一个命令)

CALL proc(3,@sname)

SET @sname='李四'

SELECT @sname


-- in 表示输入参数  mysql 输入参数是默认的 不显示的定义参数的类型 默认是输入参数
-- in 类型的参数可以写  可以省略

DELIMITER $$
CREATE PROCEDURE proc(IN i INT , OUT sname VARCHAR(20))
BEGIN
  SELECT stuName INTO sname FROM student WHERE stuId=i;
END $$

-- inout(输入输出参数)
DELIMITER $$
CREATE PROCEDURE proc(i INT , INOUT sname VARCHAR(20))
BEGIN
  SELECT CONCAT('输入进来的数据:',sname);
  SELECT stuName INTO sname FROM student WHERE stuId=i;
END $$

SET @sname='张无忌'

CALL proc(3,@sname)

SELECT @sname

-- 控制流程
-- if

-- if(){}
DELIMITER $$
CREATE PROCEDURE proc(i INT)
BEGIN
  IF i>10 THEN
    SELECT '大于10';
  END IF;
END $$

-- if else
DELIMITER $$
CREATE PROCEDURE proc(i INT)
BEGIN
  IF i>10 THEN
    SELECT '大于10';
  ELSE
    SELECT '小于等于10';
  END IF;
END $$


-- if else if else
DELIMITER $$
CREATE PROCEDURE proc(i INT)
BEGIN
  IF i<10 THEN
    SELECT '大于10';
  ELSEIF i=10 THEN
    SELECT '等于10';
  ELSE
    SELECT '小于10';
  END IF;
END $$

-- if作为函数使用

SELECT IF(10>20,'hello','world')

-- 注意 Java = 赋值运算符  == 关系运算符

-- MYSQL = 关系运算符  set = 赋值运算符

-- switch 作为等额判断  if 范围判断
DELIMITER $$
CREATE PROCEDURE proc(i INT)
BEGIN
  CASE 
  WHEN i=1 THEN SELECT '星期一';
  WHEN i=2 THEN SELECT '星期二';
  WHEN i=3 THEN SELECT '星期三';
  WHEN i=4 THEN SELECT '星期四';
  WHEN i=5 THEN SELECT '星期五';
  WHEN i=6 THEN SELECT '星期六';
  WHEN i=7 THEN SELECT '星期天';
  END CASE;
END $$


-- 循环
-- while
-- 从1 累加到指定的数
DELIMITER $$
CREATE PROCEDURE proc(i INT , OUT result INT)
BEGIN
  DECLARE n INT DEFAULT 1;
  SET result=0;
  -- while 循环  do 循环体的开始(先判断在执行)
  WHILE n<=i DO
     SET result=result+n;
     SET n=n+1;
  END WHILE;
END $$


-- loop 循环
DELIMITER $$
CREATE PROCEDURE proc(i INT , OUT result INT)
BEGIN
   DECLARE n INT DEFAULT 1;
   SET result=0;
   -- 先定义一个循环块(先执行后判断)
   haha:LOOP
      SET result=result+n;
      SET n=n+1;
      -- 当 n>i 时候  leave(离开) 定义循环块
      IF n>i THEN 
	LEAVE haha; 
      END IF;
   END LOOP;
END $$


-- repeat循环
DELIMITER $$
CREATE PROCEDURE proc(i INT , OUT result INT)
BEGIN
   DECLARE n INT DEFAULT 1;
   SET result=0;
   -- 重复
   REPEAT
      SET result=result+n;
      SET n=n+1;
      -- 直到 n>i 结束重复
      UNTIL n>i 
   END REPEAT;
END $$


CALL proc(20,@result)

SELECT @result

DROP PROCEDURE IF EXISTS proc

-- 游标
-- 游标不是数据对象(游标是一种数据类型) 
-- 存储的是数据表中一行的数据(数据库数据的结构采用二维表的数据结构)
-- 指向行的一个指针


DROP PROCEDURE IF EXISTS proc

DELIMITER $$
CREATE PROCEDURE proc()
BEGIN
  -- 定义变量来接收游标读取的数据
  DECLARE sid INT;
  DECLARE sname VARCHAR(20);
  DECLARE sex CHAR(4);
  DECLARE age INT;
  DECLARE cid INT;
  -- 定义了一个游标类型的变量 mycursor 并说明当前游标指向 select * from student 这个结果集
  DECLARE mycursor CURSOR FOR SELECT * FROM student;
  -- 打开游标(真正的产生结果集)
  OPEN mycursor;
  -- 抓取游标中指向的行的数据(并保存在定义的变量中)
  FETCH mycursor INTO sid,sname,sex,age,cid;
  SELECT sid,sname,sex,age,cid;
END $$
  
 
DELIMITER $$
CREATE PROCEDURE proc()
BEGIN
  DECLARE sid INT;
  DECLARE sname VARCHAR(20);
  DECLARE sex CHAR(4);
  DECLARE age INT;
  DECLARE cid INT;
  -- 定义游标找不到数据时的标识
  DECLARE done BOOLEAN DEFAULT FALSE;
  -- 定义游标
  DECLARE mycursor CURSOR FOR SELECT * FROM student;
  -- 定义游标找不到数据时的处理方式
  DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;
  OPEN mycursor;
  REPEAT
    FETCH mycursor INTO sid,sname,sex,age,cid;
    SELECT sid,sname,sex,age,cid;
    UNTIL done 
    END REPEAT;
END $$


-- 函数
-- 1:系统函数
-- abs 取指定数据的绝对值
SELECT ABS(-10)
-- 返回指定数值的二进制
SELECT BIN(10)
-- 返回指定数值的八进制
SELECT OCT(10)
-- 返回指定数值的十六进制
SELECT HEX(20)
-- 返回不小于指定数值的最小整数
SELECT CEILING(10.33)
-- 返回不大于指定数值的最大整数
SELECT FLOOR(10.22)
-- 返回以e为底数的x次方
SELECT EXP(2)
-- 返回指定值列表中的最大值
SELECT GREATEST(1,2,3,4,5,6,7)
-- 返回指定值列表中的最小值
SELECT LEAST(7,2,3,1,4,5)
-- 返回x的以e为底数自然对数
SELECT LN(7.38905609893065)
-- 返回为3为底数的9的对数 
SELECT LOG(3,9)
-- 返回10求3的余数
SELECT MOD(10,3)
-- 返回pi的值
SELECT PI()
-- 返回0--1之间的随机数
SELECT RAND()
-- 直接去除小数位(也可以设置保留的小数位) 设定一个参数 小数位位0  四舍五入
SELECT ROUND(10.3363,2)
-- 返回指定数值的符号数(负数的-1  正数 1 0 返回0)
SELECT SIGN(-100)
-- 返回指定数值的平方根
SELECT SQRT(9)
-- 返回指定数值的指定的小数位 不会四舍五入
SELECT TRUNCATE(10.34567,2)

-- 字符串函数
-- 返回指定字符的ascii码
SELECT ASCII('a')
-- 返回指定字符的占位数
SELECT BIT_LENGTH('你')
-- 连接指定的字符串列表
SELECT CONCAT('hello','world','你好')
-- 以-链接指定的所有的字符串
SELECT CONCAT_WS('-','hello','world','你好','世界')
-- 把hello中的从第一个字符开始到第3个字符结束的字符替换成aa
SELECT INSERT('hello',1,3,'aa')
-- 查找world在第二个字符串中出现的位置(第二个字符串 是多个字符串组成中间以都好隔开)
SELECT FIND_IN_SET('world','你好,hello,world,世界')
-- 把大写的字符串全部转换成小写
SELECT LCASE('ABCD')
SELECT LOWER('ABCD')
-- 把小写字符串全部转换成大写
SELECT UCASE('abcd')
SELECT UPPER('abcd')
-- 返回hello 中从左边开始的3个字符 
SELECT LEFT('hello',3)
-- 返回hello中从右边开始的3个字符
SELECT RIGHT('hello',3)
-- 计算指定字符串的长度
SELECT LENGTH('hello')
SELECT LENGTH('     hello')
-- 去掉左边的空格
SELECT LENGTH(LTRIM('     hello   '))
-- 去掉右边的空格
SELECT LENGTH(RTRIM('   hello   '))
-- 去掉前后的所有的空格
SELECT LENGTH(TRIM('   he   llo   '))
-- 计算ll在hello中出现的位置
SELECT POSITION('ll' IN 'hello')
-- 把指定的字符串中出现的单引号用转义字符转译
SELECT QUOTE('h''j')
-- 把hello 重复3次
SELECT REPEAT('hello',3)
-- 把指定的字符串逆序
SELECT REVERSE('hello')
-- 如果第一参数的字符串小于第二个参数的字符串 返回-1
-- 如果第一个参数的字符串大于第二个参数的字符串 返回1
-- 如果第一个参数的字符串等于第二个参数的字符串 返回0
SELECT STRCMP('abc','efd')

-- 日期函数
-- 返回当前系统的日期
SELECT CURDATE()
SELECT CURRENT_DATE()

-- 返回当前系统的时间
SELECT CURTIME()
SELECT CURRENT_TIME()

-- 返回当前系统的日期和时间
SELECT NOW()

-- 在指定的时间的部分上加上指定的数额
SELECT DATE_ADD(CURDATE(),INTERVAL 3 MONTH)
SELECT DATE_ADD(CURDATE(),INTERVAL 3 YEAR)
SELECT DATE_ADD(CURDATE(),INTERVAL 3 DAY)
SELECT DATE_ADD(CURTIME(),INTERVAL 3 HOUR)
SELECT DATE_ADD(CURTIME(),INTERVAL 3 MINUTE)
SELECT DATE_ADD(CURTIME(),INTERVAL 3 SECOND)

-- SimpleDateformat 格式化当前指定的时间
SELECT DATE_FORMAT(NOW(),'%Y年%m月%d')
-- 在指定的日期的指定的部分上减指定的数额
SELECT DATE_SUB(CURDATE(),INTERVAL 3 MONTH)

-- 计算指定的日期在当前周中是第几天
SELECT DAYOFWEEK(CURDATE())
SELECT DAYOFWEEK(DATE_ADD(CURDATE(),INTERVAL 1 DAY))

-- 指定的日期在当前月是第几天
SELECT DAYOFMONTH(CURDATE())

-- 计算指定的日期在当前年中是第几天
SELECT DAYOFYEAR(CURDATE())

-- 返回指定日期的星期名
SELECT DAYNAME(CURDATE())

-- 返回指定日期的月份名
SELECT MONTHNAME(CURDATE())

-- 返回指定时间的小时数
SELECT HOUR(CURTIME())
-- 返回指定时间的分钟数
SELECT MINUTE(CURTIME())
-- 返回指定时间的秒数
SELECT SECOND(CURTIME())
-- 返回指定日期的月
SELECT MONTH(CURDATE())
-- 返回指定日期的年
SELECT YEAR(CURDATE())
-- 返回指定日期的天
SELECT DAY(CURDATE())
-- 返回当前日期是当前年中的第多少周
SELECT WEEK(CURDATE())

-- 加密函数
SELECT DECODE('a111','aa')

SELECT ENCRYPT('a111','bb')

SELECT ENCODE('a111','vv')

-- md5(典型的加密算法)
SELECT MD5('a111')

SELECT PASSWORD('a111')

CREATE TABLE users(
    userId INT,
    userName VARCHAR(20),
    userPwd VARCHAR(100),
    PRIMARY KEY(userId)
)

INSERT INTO users VALUES(1,'admin',PASSWORD('a111'))

SELECT * FROM users

UPDATE users SET userPwd=PASSWORD('b222') WHERE userName='admin'

SELECT * FROM users WHERE userName='admin' AND userPwd=PASSWORD('a111')


-- 系统函数
-- 查询当前连接的数据库
SELECT DATABASE()
-- 查询当前连接的用户
SELECT USER()
SELECT SYSTEM_USER()
-- 当前连接到服务器的所有用户(数据表用来存储连接的用户)
SELECT CONNECTION_ID()

-- 查询当前mysql服务器的版本
SELECT VERSION()