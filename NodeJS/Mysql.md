# Mysql

# node连接Mysql

1. 下载Mysql
2. 用mysql cline client输入账号密码进入数据库使用sql语句操作数据库
3. 安装navicat界面化操作数据库

## **连接Mysql**

1. 安装插件

   ```javascript
   cnpm install mysql
   ```

2. 创建mysql连接对象

   ```javascript
   //引入mysql模块
   let mysql = require('mysql')
   
   //mysql.createConnection(options)  创建连接数据库对象
   //保存连接的配置信息
   let options = {
       host     : 'localhost',
       user     : 'root',
       password : '123456',
       database : 'wtw'  //database连接wtw数据库
       //port: 3306  修改端口号，不设置进入数据库默认端口号
   }
   
   //根据配置信息创建一个连接对象connection
   let connection = mysql.createConnection(options);
   ```

   3.连接数据库

   ```javascript
   //用连接对象连接数据库
   connection.connect((err)=>{
       if(err){
           console.log('连接失败',err)
       }else{
           console.log('连接成功')
       }
   });
   ```

   4.执行sql语句与回调函数

   ```javascript
   connectionObj.query(sqlStr,callback)
   ```

   ```javascript
   //执行sql语句并执行回调
   //常见的回调基本如下
   let sqlStr = ''
   
   connection.query(sqlStr,(err)=>{
   if(err){
     console.log(err)
   }else{
     console.log('操作成功')
    }
   })
   ```
   
   5.关闭连接结束进程
   
   ```javascript
   connection.end()
   ```
   
   

## 占位符

```javascript
//sql语句往往不能写死，先用占位符 ? 占位，然后需要从前端拿到数据填充
//使用占位符后，query方法参数变为三个，sql语句，填充数据数组，回调函数
let sqlStr8 = "insert into user (username,password) values (?,?)"
connection.query(sqlStr8, ['小明','123456'], (err)=>{
  if(err){
    console.log(err)
  }else{
    console.log('插入成功')
  }
})
```

   

# 建库 create database

```sql
create database shop;
```



# 删库 drop database

```javascript
drop database shop;
```



# 建表 create table

```sql
CREATE TABLE `TEACHER`(`id` int NOT NULL AUTO_INCREMENT ,`tName` varchar(255) NULL,`age` varchar(255) NULL,PRIMARY KEY(`id`));
```



# 删表 drop table

```sql
drop table user;
```



# id自增

id自增
当设计表时，给主键id设置了自动递增，就可以加行时省略设置id，id将从自动1开始递增



# 插入数据 insert into

```sql
insert into user (id,username,password) values (1,'wtw','123456');
```



# 更新 update set

update 表 set 属性=值 where 查询条件

将查询出来的目标的指定属性修改为对应的值

```sql
update users set realname='abc' where username='lisi';
-- 将username为lisi的行的realname改为abc
```



# 删行 delete

```sql
delete from users where username="zhangsan"
-- 删除username为zhangsan的那一行
```



# 查 select

查询的sql语句会返回一个数组，里面包含所有的结果，就算只有一个结果也是一个数组

```javascript
   //查询表user所有信息
   //查询的回调有三个参数错误信息err，输出查询结果results，查询到的相关字段fields
let sqlStr = 'select * from user'
connection.query(sqlStr,(err,results,fields)=>{
   //err报错信息，results查询出的结果，fields查询到的相关字段
   if(err){
     console.log(err)
   }else{
     console.log(results)
     console.log(fields)
  }
})
```



## 筛选查询where

```
SELECT 输出字段 FROM 表名 WHERE 查询条件
```

例如查询成绩大于60小于90的学生全部信息

```sql
SELECT * FROM 表名 WHERE score > 60 AND score < 90
```

逻辑词：AND，OR，NOT

运算符：!=，=，>，>=，<，<=，!=，<>（不等于）

或OR，可以替换为`IN ()`

```sql
SELECT * from 表名 WHERE score = 60 OR score = 90
SELECT * from 表名 WHERE score IN (60,90)
```



### where 1 = 1

`selete * from blogs where 1 = 1`可以用于占位，1=1永远正确，防止没有条件拼接时sql语句报错

例如拼接sql语句时，后面没有任何语句跟他拼接`selete * from blogs where`后面没有内容就会直接报错。加上1=1是让他没有查询条件时也不会导致报错



## 模糊查找like

- like 模糊查询

- %表示任意多个字符

- _表示一个任意字符

  

  查询和股票相关

```sql
SELECT * FROM book WHERE bookname LIKE '%股票%'
```



## 范围查找

- in 表示在一个集合内

  查找成绩等于60或者90的人

  ```sql
  SELECT * from 表名 WHERE score IN (60,90)
  ```

- between...and...  表示在一个连续的范围内

  查找成绩等于60-90的人

  ```sql
  SELECT * from 表名 WHERE score between 60 and 90
  ```

- is NULL  查找该属性为空

  ```sql
  SELECT * from 表名 WHERE score is NULL
  ```




##  排序order by

排序默认为正序，最后加desc为倒序

```sql
SELECT * FROM book WHERE bookname LIKE '%股票%' order by id;
-- 根据id正序所有关于股票的内容
SELECT * FROM book WHERE bookname LIKE '%股票%' order by id desc;
-- 根据id倒序所有关于股票的内容
```



# sql语句

```sql
use myblog;
show tables; --查询所有表
insert into users(username, `password`, realname) values ('zhangsan','123','张三');
insert into users(username, `password`, realname) values ('lisi','123','李四');
-- 插入一个数据
-- password为关键字，需要用``包裹使用
select id,username from users;
```



