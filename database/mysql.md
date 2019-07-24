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

## 1 查

### 1.1 条件查询

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

### 1.2 聚合查询

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

### 1.3 分组查询

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

### 1.4 排序查询

- 语法：

  ```mysql
  select * from 表名 order by 列1 asc|desc,列2 asc|desc,...
  ```

- 实例

  ```mysql
  SELECT id, name, gender, score FROM students ORDER BY score;( ORDER BY score DESC倒序;)
  ```

### 1.5 分页查询

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

### 1.6 投影查询

- 显示查询时将score重命名为points

  ```mysql
  select id, score points, name from students;
  ```

  ```mysql
  select * from cinema where description != 'boring' and id % 2 = 1 order by rating desc;单引号+‘=’
  ```

### 1.7 连接查询

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
```

------

## 2 增

- 表中插入数据

  ```mysql
  insert IGNORE into students (name, sex, age) values("孙丽华", "女", 21);
  ```

  - <font color=red>*Ignore* </font>忽略插入与表内UNIQUE字段都相同的记录
  - <font color=red>*Replace* </font> 更新替代与表内UNIQUE字段都相同的记录
  
------

## 3 改

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

- 更新表中的数据

  ```mysql
  update salary set sex= case sex when 'm' then 'f' else 'm' end;
  ```

  ```mysql
  update titles_test set to_date=NULL,from_date='2001-01-01' where to_date='9999-01-01';
  # NULL不能改为''
  ```

------

## 4 删

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

- 删除表中的数据

  ```mysql
  delete from students where id=2;
  ```

- 删除列

  基本形式：alter table 表名 drop 列名称;

  ```mysql
  alter table students drop birthday;
  ```

------

## 5 视图

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

  

