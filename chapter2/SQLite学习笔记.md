

SQL 语句后面的分号？

某些数据库系统要求在每条 SQL 命令的末端使用分号。在我们的教程中不使用分号。

分号是在数据库系统中分隔每条 SQL 语句的标准方法，这样就可以在对服务器的相同请求中执行一条以上的语句。

如果您使用的是 MS Access 和 SQL Server 2000，则不必在每条 SQL 语句之后使用分号，不过某些数据库软件要求必须使用分号。


[SQLite 基础教程](https://www.twle.cn/l/yufei/sqlite/sqlite-basic-attach-database.html)

[SQLite权威指南](https://www.kancloud.cn/wizardforcel/the-definitive-guide-to-sqlite/154155)

[7.3.1 SQLite及SQLiteDatabase家族](https://www.kancloud.cn/alex_wsc/android-deep2/413513)  具体程序讲解




#### 表的结构

sceneId repeat time power brightness temperature 


CREATE TABLE scene (
   id TEXT PRIMARY KEY     NOT NULL,
   repeat         TEXT    NOT NULL,
   time           TEXT    NOT NULL,
   power          INT     NOT NULL,
   brightness          INT     NOT NULL,
   temperature          INT     NOT NULL
);


INSERT INTO scene (id,repeat,time,power,brightness,temperature)
VALUES ("aaaaaaaa", '1111111', "18:08", 1, 200,500);


UPDATE scene SET ena = 1 WHERE id = "abcdefgh";
abcdefgh|0|127|8|0|1|80|2700

SELECT COUNT(*) FROM scene WHERE

DELETE FROM " + TABLE_NAME + " WHERE " + KEY + "=?;

DELETE FROM " scene WHERE id = "abcdefgh"



abcdefgh

32393135|1|127|8|56|1|0|2700
171     }else{                  
172         const std::string sqlString = "INSERT INTO " + TABLE_NAME + " (" +
173                                 "id, enable, week, hour, minute," + 
174                                 "power, brightness, temperature" 
175                                 ") VALUES (" +
176                                 "?, ?, ?, ?, ?," +
177                                 "?, ?, ?" 
178                                 ");";


UPDATE scene SET enable=1, week=0, hour=17, WHERE id = "32393135";

UPDATE scene SET enable=1, week=1, hour=1, minute=1, power=1, brightness=1,temperature=1 WHERE id="32393135";

UPDATE scene SET enable=1, week=1, hour=1, minute=1, power=1, brightness=1, temperature=1 WHERE id="32393135";



[常用工具类之C++线程安全集合类](https://blog.csdn.net/cuglifangzheng/article/details/50846289)


 