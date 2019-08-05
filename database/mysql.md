# mysql

---

 1. 获取Employees中的first_name，查询按照first_name最后两个字母，按照升序进行排列
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

----------

`本题考查 substr(X,Y,Z) 或 substr(X,Y)函数的使用。其中X是要截取的字符串。Y是字符串的起始位置（注意第一个字符的位置为1，而不为0），取值范围是±(1~length(X))，当Y等于length(X)时，则截取最后一个字符；当Y等于负整数-n时，则从倒数第n个字符处截取。Z是要截取字符串的长度，取值范围是正整数，若Z省略，则从Y处一直截取到字符串末尾；若Z大于剩下的字符串长度，也是截取到字符串末尾为止。`

## 1 SQL语言分类

### 简介

可以使用下面的命令查看已经安装的MySQL相关的包。

```Shell
rpm -qa | grep mysql
```

* 启动MySQL服务。

  启动MySQL成功后，可以通过下面的命令来检查网络端口使用情况，MySQL默认使用3306端口。

  ```Shell
  netstat -nap | grep mysql
  ```

  也可以使用下面的命令查找是否有名为mysqld的进程。

  ```Shell
  pgrep mysqld
  ```

  如果是首次安装MySQL，可以使用下面的命令来找到默认的初始密码。

  ```Shell
  cat /var/log/mysqld.log | grep password
  ```

  上面的命令会查看MySQL的日志带有password的行，在显示的结果中`root@localhost:`后面的部分就是默认设置的初始密码。

  修改超级管理员（root）的访问口令为`123456`。

  ```python
  set global validate_password_policy=0;
  set global validate_password_length=6;
  alter user 'root'@'localhost' identified by '123456';
  ```

2. 常用命令。

   * 查看服务器版本。

     ```SQL
     select version();
     ```

   * 获取帮助。

     ```SQL
     ? contents;
     ? functions;
      ? numeric functions;
     ? round;
      
     ? data types;
     ? longblob;
     ```

### 1.1 **数据查询语言DQL**

数据查询语言DQL基本结构是由SELECT子句，FROM子句，WHERE
子句组成的查询块：
SELECT <字段名表>
FROM <表或视图名>
WHERE <查询条件>

### 1.2 **数据操纵语言DML**

数据操纵语言DML主要有三种形式：
1) 插入：INSERT
2) 更新：UPDATE
3) 删除：DELETE

### 1.3 **数据定义语言DDL**

数据定义语言DDL用来创建数据库中的各种对象-----表、视图、
索引、同义词、聚簇等如：
CREATE TABLE/VIEW/INDEX/SYN/CLUSTER
| | | | |
表 视图 索引 同义词 簇

DDL操作是隐性提交的！不能rollback

### 1.4 **数据控制语言DCL**

数据控制语言DCL用来授予或回收访问数据库的某种特权，并控制
数据库操纵事务发生的时间及效果，对数据库实行监视等。如：

1) GRANT：授权。

2) ROLLBACK [WORK] TO [SAVEPOINT]：回退到某一点。
回滚---ROLLBACK
回滚命令使数据库状态回到上次最后提交的状态。其格式为：
SQL>ROLLBACK;

3) COMMIT [WORK]：提交。

在数据库的插入、删除和修改操作时，只有当事务在提交到数据库时才算完成。在事务提交前，只有操作数据库的这个人才能有权看到所做的事情，别人只有在最后提交完成后才可以看到。
提交数据有三种类型：显式提交、隐式提交及自动提交。

1. 显式提交

   用COMMIT命令直接完成的提交为显式提交。其格式为：
   SQL>COMMIT；

2. 隐式提交

   用SQL命令间接完成的提交为隐式提交。这些命令是：
   ALTER，AUDIT，COMMENT，CONNECT，CREATE，DISCONNECT，DROP，
   EXIT，GRANT，NOAUDIT，QUIT，REVOKE，RENAME。

3. 自动提交

   若把AUTOCOMMIT设置为ON，则在插入、修改、删除语句执行后，
   系统将自动进行提交，这就是自动提交。其格式为：
   SQL>SET AUTOCOMMIT ON；

------

## 2 SQL增、删、改、查

### 2.1 查

#### 2.1.1 条件查询

- #### 模糊查询

  - like

  - %：任意多个字符

  - _: 任意一个字符

  - 查询姓黄的学生

    ```mysql
    select * from students where sname like '黄%';
    ```

  - 查询姓黄并且名字是一个字的学生

    ```mysql
    select * from students where sname like '黄_';
    ```

  - 查询姓黄或叫靖的学生

    ```mysql
    select * from students where sname like '黄%' or sname like '%靖%';
    ```

- 范围查询

  - in表示在一个非连续的范围内

  - 查询编号是1或3或8的学生

    ```mysql
    select * from students where id in(1,3,8);
    ```

  - between ... and ...表示在一个连续的范围内

  - 查询学生是3至8的学生

    ```mysql
    select * from students where id between 3 and 8;
    ```

- 空判断

  - null与''是不同的
  
  - 判空is null

  - 查询没有填写地址的学生

    ```mysql
    select * from students where hometown is null;
    ```
  
- 综合运用

  ```mysql
  SELECT * FROM students WHERE (score < 80 OR score > 90) AND gender = 'M'; (and\or\not)   between 25 and 30/in/not in
  ```

  ```mysql
  重命名SELECT 列1 别名1, 列2 别名2, 列3 别名3 FROM . SELECT id, score points, name FROM students;
  ```

  ```mysql
  select * from students where sex>="女";
  ```

- 优先级

  - 小括号，not，比较运算符，逻辑运算符
  - <font color=red>and比or先运算</font>，如果同时出现并希望先算or，需要结合()使用

#### 2.1.2 聚合查询

使用聚合函数进行查询，就是聚合查询

**聚合函数**

1. **SUM**    **计算某一列的合计值，该列必须为数值类型**

2. **AVG**     **计算某一列的平均值，该列必须为数值类型**

3. **MAX**    **计算某一列的最大值（不限于数值类型）**

4. **MIN**     **计算某一列的最小值（不限于数值类型）**

   ------

* 查询students表中一共有多少记录

```mysql
select count(*) from students;
```

- 给列名count设置一个别名num

```mysql
select count(*) num from students; 
```

- 查询gender为m的记录

```mysql
select count(*) from students where gender='m'; 
```

- 查询男生的平均成绩

```mysql
select avg(score) from sudents where gender='m';
```

**注意**

*如果聚合查询的WHERE条件没有匹配到任何行，COUNT()会返回0，而MAX()、MIN()、MAX()和MIN()会返回NULL*

- 查询所有超过或等于5名学生的课

  ```mysql
  select classs from courses group by class having count(distinct student) >= 5
  ```

- substr()获取Employees中的first_name，查询按照first_name最后两个字母，按照升序进行排列

  - <font color=red>substr(X,Y,Z) 或substr(X,Y)函数的使用</font>

    其中X是要截取的字符串。Y是字符串的起始位置（注意第一个字符的位置为1，而不为0），取值范围是±(1~length(X))，当Y等于length(X)时，则截取最后一个字符；当Y等于负整数-n时，则从倒数第n个字符处截取。Z是要截取字符串的长度，取值范围是正整数，若Z省略，则从Y处一直截取到字符串末尾；若Z大于剩下的字符串长度，也是截取到字符串末尾为止。

  ```mysql
  select first_name from Employees order by substr(first_name,length(first_name)-1);
  ```

#### 2.1.3 分组查询

- 分组后的数据筛选

  - 语法：

    ```mysql
    select 列1,列2,聚合... from 表名 group by 列1,列2,列3...
    ```

  - having后面的条件运算符与where的相同

  - 查询男生总人数

    ```mysql
    方案一
    select count(*)
    from students
    where gender=1;
    -----------------------------------
    方案二：
    select gender as 性别,count(*)
    from students
    group by gender
    having gender=1;
    ```

- 根据gender进行分组显示数量

  ```mysql
  select count(*) from students group by gender;
  ```
  
- 根据class_id进行分组显示数量和class_id

  ```mysql
  select count(*),class_id from students group by class_id;
  ```

- 根据多个条件进行分组(先根据class_id分组，后根据gender分组)

  ```mysql
  select count(*),gender,class_id from students group by class_id,gender;
  ```

- 对比where和having

  - where是对from后面指定的表进行数据筛选，属于对原始数据的筛选
  - having是对group by的结果进行筛选

#### 2.1.4 排序查询

- 语法：

  ```mysql
  select * from 表名 order by 列1 asc|desc,列2 asc|desc,...
  ```

- 实例

  ```mysql
  SELECT id, name, gender, score FROM students ORDER BY score;( ORDER BY score DESC倒序;)
  ```

#### 2.1.5 分页查询

- 查询结果每页最多3条，显示第1面（注意索引从0开始）

  ```mysql
  select * from students limit 3 offset 0;
  ```

- 查询结果每页最多4条，显示第2面

  ```mysql
  select * from students limit 4 offset 1;
  ```

  - OFFSET计算公式为<font color=red>pageSize * (pageIndex - 1)</font>。
  - `OFFSET`超过了查询的最大数量并不会报错，而是得到一个空的结果集
  - 在MySQL中，<font color=red>LIMIT 15 OFFSET 30</font>还可以简写成<font color=red>LIMIT 30, 15</font>
  - 使用<font color=red>LIMIT <M> OFFSET <N></font>分页时，随着N越来越大，查询效率也会越来越低

#### 2.1.6 投影查询

- 显示查询时将score重命名为points

  ```mysql
  select id, score points, name from students;
  ```

  ```mysql
  select * from cinema where description != 'boring' and id % 2 = 1 order by rating desc;单引号+‘=’
  ```

#### 2.1.7 连接查询

- INNER JOIN查询的写法：

  1. 先确定主表，仍然使用FROM <表1>的语法；

  2. 再确定需要连接的表，使用INNER JOIN <表2>的语法；

  3. 然后确定连接条件，使用ON <条件...>，这里的条件是s.class_id = c.id，表示students表的class_id列与classes表的id列相同的行需要连接；

  4. 可选：加上WHERE子句、ORDER BY等子句。
     这是内查询INNER JOIN，只返回同时存在于两张表的行数据
     还有外查询,外查询分为RIGHT OUTER JOIN，LEFT OUTER JOIN和FULL OUTER JOIN。它们的区别是：

     •	RIGHT OUTER JOIN返回右表都存在的行。如果某一行仅在右表存在，那么结果集就会以NULL填充剩下的字段。
     •	LEFT OUTER JOIN则返回左表都存在的行。如果某一行仅在左表存在，那么结果集就会以NULL填充剩下的字段。
     •	FULL OUTER JOIN会把两张表的所有记录全部选择出来，并且，自动把对方不存在的列填充为NULL

```mysql
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
INNER JOIN classes c
ON s.class_id = c.id;

-- 查询学生姓名、课程名称以及成绩按成绩从高到低查询第11-15条记录(内连接+分页)
select stuname, couname, score from tb_student inner join tb_record on stuid=sid inner join tb_course on couid=cid where score is not null order by score desc limit 5 offset 10;

select stuname, couname, score from tb_student inner join tb_record on stuid=sid inner join tb_course on couid=cid where score is not null order by score desc limit 10, 5;
```

------

### 2.2 增

数据定义语言DDL用来创建数据库中的各种对象-----表、视图、索引、同义词、聚簇等如：
<font color=red>CREATE TABLE/VIEW/INDEX/SYN/CLUSTER</font>            表/视图/索引/同义词/簇

```mysql
-- 如果存在名为school的数据库就删除它
drop database if exists school;

-- 创建名为school的数据库并设置默认的字符集和排序方式
create database school default charset utf8 collate utf8_bin;

-- 切换到school数据库上下文环境
use school;

-- 创建学院表
create table tb_college
(
collid		int auto_increment comment '编号',
collname	varchar(50) not null comment '名称',
collmaster	varchar(20) not null comment '院长',
primary key (collid)
);

-- 创建学生表
create table tb_student
(
stuid		int not null comment '学号',
stuname		varchar(20) not null comment '姓名',
stusex		boolean default 1 comment '性别',
stubirth	date not null comment '出生日期',
stuaddr		varchar(255) default '' comment '籍贯',
collid		int not null comment '所属学院',
primary key (stuid),
foreign key (collid) references tb_college (collid)
);

-- 创建教师表
create table tb_teacher
(
teaid		int not null comment '工号',
teaname		varchar(20) not null comment '姓名',
teatitle	varchar(10) default '助教' comment '职称',
collid		int not null comment '所属学院',
primary key (teaid),
foreign key (collid) references tb_college (collid)
);

-- 创建课程表
create table tb_course
(
couid		int not null comment '编号',
couname		varchar(50) not null comment '名称',
coucredit	int not null comment '学分',
teaid		int not null comment '授课老师',
primary key (couid),
foreign key (teaid) references tb_teacher (teaid)
);

-- 创建选课记录表
create table tb_record
(
recid		int auto_increment comment '选课记录编号',
sid			int not null comment '选课学生',
cid			int not null comment '所选课程',
seldate		datetime default now() comment '选课时间日期',
score		decimal(4,1) comment '考试成绩',
primary key (recid),
foreign key (sid) references tb_student (stuid),
foreign key (cid) references tb_course (couid),
unique (sid, cid)
);
```

- 表中插入数据(数据操纵语言DML)

  - <font color=red>插入：INSERT</font>
  
  ```mysql
insert IGNORE into students (name, sex, age) values("孙丽华", "女", 21);
  ```
  
  - <font color=red>*Ignore* </font>忽略插入与表内UNIQUE字段都相同的记录
  - <font color=red>*Replace* </font> 更新替代与表内UNIQUE字段都相同的记录
  
------

### 2.3 改

- 添加列

  基本形式：alter table 表名 add 列名 列数据类型 [after 插入位置];

  **在名为 age 的列后插入列 birthday**

  ```mysql
  alter table students add birthday date after age;
  ```

- 修改列

  基本形式: alter table 表名 change 列名称 列新名称 新数据类型;

  将表 tel 列改名为 telphone；

  ```mysql
  alter table students change tel telphone char(13) default "-";
  ```

  将 name 列的数据类型改为 char(16): 

  ```mysql
  alter table students change name name char(16) not null;
  ```

- 重命名表

  基本形式: alter table 表名 rename 新表名;

  ```mysql
  alter table students rename workmates;
  ```

- 更新表中的数据(数据操纵语言DML)

  数据操纵语言DML三种形式之一：
  
  - <font color=red>更新：UPDATE</font>

  ```mysql
  update salary set sex= case sex when 'm' then 'f' else 'm' end;
  ```
  
  ```mysql
  update titles_test set to_date=NULL,from_date='2001-01-01' where to_date='9999-01-01';
  # NULL不能改为''
  ```

------

### 2.4 删

- 删除整个数据库

  基本形式：drop database 数据库名；

  ```mysql
  drop database samp_db;
  ```

- 删除整张表

  基本形式：drop table 表名;

  ```mysql
  drop table workmates;
  ```

- 删除表中的数据(DML)

  数据操纵语言DML三种形式之一：

  - <font color=red>删除：DELETE</font>

  ```mysql
  delete from students where id=2;
  ```

- 删除列

  基本形式：alter table 表名 drop 列名称;

  ```mysql
  alter table students drop birthday;
  ```

------

### 2.5 视图

- 视图本质就是对查询的一个封装

- 定义视图

  ```mysql
  create view stuscore as 
  select students.*,scores.score from scores
  inner join students on scores.stuid=students.id;
  ```

  ```mysql
  Create view v_emp(v_name,v_age,v_phone) as select name,age,phone from employee;
  ```

- 视图的用途就是查询

  ```mysql
  select * from stuscore;
  ```

------

## 3 python交互

### 3.1 交互类型

#### 3.1.1 安装模块

- 安装mysql模块

```shell
pip install pymysql
```

#### 3.1.2 Connection对象

* 用于建立与数据库的连接
* 创建对象：调用connect()方法

```
conn=connect(参数列表)
```

* 参数host：连接的mysql主机，如果本机是'localhost'
* 参数port：连接的mysql主机的端口，默认是3306
* 参数db：数据库的名称
* 参数user：连接的用户名
* 参数password：连接的密码
* 参数charset：通信采用的编码方式，默认是'gb2312'，要求与数据库创建时指定的编码一致，否则中文会乱码

***对象的方法：***

* close()关闭连接
* commit()事务，所以需要提交才会生效
* rollback()事务，放弃之前的操作
* cursor()返回Cursor对象，用于执行sql语句并获得结果

#### 3.1.3 Cursor对象

* 执行sql语句
* 创建对象：调用Connection对象的cursor()方法

```
cursor1=conn.cursor()
```

***对象的方法：***

* close()关闭
* execute(operation [, parameters ])执行语句，返回受影响的行数
* fetchone()执行查询语句时，获取查询结果集的第一个行数据，返回一个元组
* next()执行查询语句时，获取当前行的下一行
* fetchall()执行查询时，获取结果集的所有行，一行构成一个元组，再将这些元组装入一个元组返回
* scroll(value[,mode])将行指针移动到某个位置
  * mode表示移动的方式
  * mode的默认值为relative，表示基于当前行移动到value，value为正则向下移动，value为负则向上移动
  * mode的值为absolute，表示基于第一条数据的位置，第一条数据的位置为0

***对象的属性：***

* rowcount只读属性，表示最近一次execute()执行后受影响的行数
* connection获得当前连接对象

### 3.2 增改删

#### 3.2.1 增

```python
import pymysql


def main():
    no = int(input('编号: '))
    name = input('名字: ')
    loc = input('所在地: ')
    # 1. 创建数据库连接对象
    con = pymysql.connect(host='localhost', port=3306,
                          database='hrs', charset='utf8',
                          user='root', password='123456')
    try:
        # 2. 通过连接对象获取游标
        with con.cursor() as cursor:
            # 3. 通过游标执行SQL并获得执行结果
            result = cursor.execute(
                'insert into tb_dept values (%s, %s, %s)',
                (no, name, loc)
            )
        if result == 1:
            print('添加成功!')
        # 4. 操作成功提交事务
        con.commit()
    finally:
        # 5. 关闭连接释放资源
        con.close()


if __name__ == '__main__':
    main()
```

#### 3.2.2 改

```python
import pymysql


def main():
    no = int(input('编号: '))
    name = input('名字: ')
    loc = input('所在地: ')
    con = pymysql.connect(host='localhost', port=3306,
                          database='hrs', charset='utf8',
                          user='root', password='123456',
                          autocommit=True)
    try:
        with con.cursor() as cursor:
            result = cursor.execute(
                'update tb_dept set dname=%s, dloc=%s where dno=%s',
                (name, loc, no)
            )
        if result == 1:
            print('更新成功!')
    finally:
        con.close()


if __name__ == '__main__':
    main()
```

#### 3.2.3 删

```python
import pymysql


def main():
    no = int(input('编号: '))
    con = pymysql.connect(host='localhost', port=3306,
                          database='hrs', charset='utf8',
                          user='root', password='123456',
                          autocommit=True)
    try:
        with con.cursor() as cursor:
            result = cursor.execute(
                'delete from tb_dept where dno=%s',
                (no, )
            )
        if result == 1:
            print('删除成功!')
    finally:
        con.close()


if __name__ == '__main__':
    main()
```

#### 3.2.4 查

查询所有部门

```python
import pymysql
from pymysql.cursors import DictCursor


def main():
    con = pymysql.connect(host='localhost', port=3306,
                          database='hrs', charset='utf8',
                          user='root', password='123456')
    try:
        with con.cursor(cursor=DictCursor) as cursor:
            cursor.execute('select dno as no, dname as name, dloc as loc from tb_dept')
# fetchall()执行查询时，获取结果集的所有行，一行构成一个元组，再将这些元组装入一个元组返回
            results = cursor.fetchall()
            print(results)
            print('编号\t名称\t\t所在地')
            for dept in results:
                print(dept['no'], end='\t')
                print(dept['name'], end='\t')
                print(dept['loc'])
    finally:
        con.close()


if __name__ == '__main__':
    main()
```

分页查询

```python
import pymysql
from pymysql.cursors import DictCursor


class Emp(object):

    def __init__(self, no, name, job, sal):
        self.no = no
        self.name = name
        self.job = job
        self.sal = sal

    def __str__(self):
        return f'\n编号：{self.no}\n姓名：{self.name}\n职位：{self.job}\n月薪：{self.sal}\n'


def main():
    page = int(input('页码: '))
    size = int(input('大小: '))
    con = pymysql.connect(host='localhost', port=3306,
                          database='hrs', charset='utf8',
                          user='root', password='123456')
    try:
        with con.cursor() as cursor:
            cursor.execute(
                'select eno as no, ename as name, job, sal from tb_emp limit %s,%s',
                ((page - 1) * size, size)
            )
            for emp_tuple in cursor.fetchall():
                emp = Emp(*emp_tuple)
                print(emp)
    finally:
        con.close()


if __name__ == '__main__':
    main()
```

### 3.3 封装

#### 3.3.1 整体封装

```python
#encoding=utf8
import MySQLdb

class MysqlHelper():
    def __init__(self,host,port,db,user,passwd,charset='utf8'):
        self.host=host
        self.port=port
        self.db=db
        self.user=user
        self.passwd=passwd
        self.charset=charset

    def connect(self):
        self.conn=MySQLdb.connect(host=self.host,port=self.port,db=self.db,user=self.user,passwd=self.passwd,charset=self.charset)
        self.cursor=self.conn.cursor()

    def close(self):
        self.cursor.close()
        self.conn.close()

    def get_one(self,sql,params=()):
        result=None
        try:
            self.connect()
            self.cursor.execute(sql, params)
            result = self.cursor.fetchone()
            self.close()
        except Exception, e:
            print e.message
        return result

    def get_all(self,sql,params=()):
        list=()
        try:
            self.connect()
            self.cursor.execute(sql,params)
            list=self.cursor.fetchall()
            self.close()
        except Exception,e:
            print e.message
        return list

    def insert(self,sql,params=()):
        return self.__edit(sql,params)

    def update(self, sql, params=()):
        return self.__edit(sql, params)

    def delete(self, sql, params=()):
        return self.__edit(sql, params)

    def __edit(self,sql,params):
        count=0
        try:
            self.connect()
            count=self.cursor.execute(sql,params)
            self.conn.commit()
            self.close()
        except Exception,e:
            print e.message
        return count
```

#### 3.3.2 添加

* 创建testInsertWrap.py文件，使用封装好的帮助类完成插入操作

```python
#encoding=utf8
from MysqlHelper import *

sql='insert into students(sname,gender) values(%s,%s)'
sname=raw_input("请输入用户名：")
gender=raw_input("请输入性别，1为男，0为女")
params=[sname,bool(gender)]

mysqlHelper=MysqlHelper('localhost',3306,'test1','root','mysql')
count=mysqlHelper.insert(sql,params)
if count==1:
    print 'ok'
else:
    print 'error'
```

#### 3.3.3 查询一个

- 创建testGetOneWrap.py文件，使用封装好的帮助类完成查询最新一行数据操作

```python
#encoding=utf8
from MysqlHelper import *

sql='select sname,gender from students order by id desc'

helper=MysqlHelper('localhost',3306,'test1','root','mysql')
one=helper.get_one(sql)
print one
```

