### B树

### B+树



### MySQL索引



### 索引优化





## MySQL语句

1\ 查看表的结构 desc emp；

### 查询

1、between and   ->  区间为闭区间，可以取到边界条件，除了数字，还可以用在字符串方面；

select ename,sal from emp where sal between 110 and 3000;

2、字符串用‘’来括起来，不是双引号；单引号的通用性更好；

3、重命名可以用as关键字，可以用空格代替，就是省去as

4、NULL不是一个值，不能用等号衡量；select ename,sal,comm from emp where comm is null;

5、in等同于or，in（值1，值2，值3）；不是区间；not in(值1，值2，值3……）不在这几个值中；

6、模糊查询like， %：任意多个字符，   _：任意一个字符；

7、去除重复记录：加关键字`distinct`；            select distinct job from emp;    

8、`distinct`只能出现在所有字段的最前方，并且表示的是后面所有字段联合去重，

### 排序

1、order by  默认是升序；   select ename,sal, from emp order by sal;

asc 表示升序，desc表示降序；  select ename,sal from emp order by sal desc;

select ename,sal from emp order by sal desc , ename asc;  前面的优先级高，只有前面的排序字段相等的时候，会启用第二个排序规则；

select ename,sal from emp order by 2；   按照第二列排序；

2、语句的执行顺序   from -> where -> select -> order by

### 分组函数

1、 所有的分组函数都是对某一组数据进行操作的；

2、count 计数； sum 求和； avg 平均值； min ； max ；；； select sum(sal) from emp;

3、 分组函数自动忽略NULL

4、分组函数不能直接用在where子句中；`原因是group by是在where之后执行的，而分组函数一般与group by一起用`

5、count（*）与字段无关，查询到的是记录（不为NULL的）的总数，而count（字段）查询到的是字段中不为空的元素总数；

6、分组函数可以连起来用，用逗号隔开；

7、group by：按照某个字段或者某些字段进行分组；

​	having：对分组之后的数据进行再次过滤；  select max(sal) from emp group by job;

​	分组函数一般与group by 联合起来使用；

​	当一条sql语句没有group by的时候，整个表的数据会自成一组；

8、当一条语句中有group by的话，select后面只能跟分组函数和参与分组的字段；

9、能用where先过滤的就先用where过滤，用不了再用having过滤，因为where的调用顺序靠前，能够先过滤再查询，效率更高一些

```mysql
找出每个部门不同工作岗位的最高薪资；
select 
	deptno,job,max(sal)
from 
	emp
order by 
	deptno,job;
找出每个部门的最高薪资，要求显示薪资大于2500的数据； 
select deptno,max(sal) from	emp where sal > 2500 order by deptno;//效率高
select deptno,max(sal) from emp order by deptno having sal > 2900;//效率低
找出每个部门的平均薪资，要求显示薪资大于2000的数据
select deptno,avg(sal) from emp order by deptno having avg(sal) > 2000;
```

### 执行顺序

```mysql
select      5
	..
from        1
	..
where       2
	..
order by    3
	..
having      4
	..
order by    6
	..
limit
	..      7
```

### 连接查询

根据表的连接方式来划分：

- 内连接：假设A和B表进行连接，使用内连接的话，凡是A表和B表能够匹配上的记录查询出来，AB两张表没有主副之分，两张表是平等的；

  - 等值连接：

    最大的特点是：条件是等量关系；

    ```mysql
    ...
    	A
    join            省略了inner  实际是 inner join
    	B
    on
    	连接条件(用等号，所以是等值连接)
    where	
    ```

  - 非等值连接：

    最大的特点是：连接条件中的关系是非等量关系；

    ```mysql
    select e.ename,e.sal,s.grade from emp e join salgrade s on e.sal between s.losal and s.hisal;
    ```

  - 自连接：

    最大的特点是：一张表看做两张表，自己连接自己；

    ```mysql
    select a.ename as '员工名'，b.ename as '领导名' from
    ```

- 外连接：假设A和B表进行连接，使用外连接的话，AB两张表中有一张表是主表，一张表是副表，主要查询主表中的数据，捎带着查询副表，当副表中的数据没有和主表中的数据匹配上，副表自动模拟出NULL与之匹配；

  - 左外连接：表示左边的表是主表；

    ```mysql
    select a.ename as '员工', b.ename as '领导' from emp a left  outer join emp b on a.mgr = b.empno;
    ```

    outer可以省略；

  - 右外连接：表示右边的表是主表；

  - 左连接有右连接的写法，右连接有左连接的写法； 

- 全连接：

​	在表的连接查询方面有一种现象被称为：笛卡尔积现象--->当两张表进行查询的时候，如果没有任何条件限制，最终的查询结果条数是两张表记录条数的乘积；

### 子查询

```mysql
select * from emp where sal > (select avg(sal) from emp);
```

```mysql
select t.*,s.grade from (select deptno,avg(sal) as avgsal from emp group by deptno) t join salgrade s on t.avgsal between s.losal and hisal;
```

### union

可以将查询结果集相加；常用于两张不相关的表的拼接；用的时候要保证select的列数一致；

```mysql
select ename,job from emp where job = 'MANAGER' or 'SALESMAN';

select ename job from emp where job = 'MANAGER'
union
select ename job from emp where job = 'SALESMAN';
```

### limit

1、limit是MySQL特有的；

2、limit取结果集中的部分数据，这是它的作用；

3、语法机制： limit startindex，index

​						startindex 表示起始位置；

​						length 表示取几个；

4、limit是sql语句中最后执行的一个环节；

```mysql
select ename,sal from emp order by sal desc limit 0,5;
select ename,sal from emp order by sal desc limit 5;//同上
select ename,sal from emp order by sal desc limit 3,6;//从0开始数；取4-9的记录
```

5、通用的标准分页sql

```mysql
每页显示pagesize条记录：
第pageNo页：limit （pageNo-1) * pagesize , pagesize;
```

### 表的创建

常见数据类型：

int:	整数型

bigint：	长整型

float：	浮点型

char：	定长字符串

varchar：	可变长字符串

date：	日期类型

BLOB：	二进制大对象

CLOB：	字符大对象

char类型的效率要比varchar要高很多，但是是固定长度分配，如果超出这个长度就会报错，如果输入的长度小于分配的长度，会自动补齐到对应的长度；varchar也是分配一个长度，但是如果输入的长度不足分配的长度，那么不会自动补齐，利用varchar需要判断输入的长度，所以不如char的效率高；

在实际开发中，当某个字段中的数据长度不发生改变的时候，是定长的，用char；

当一个字段的数据长度不确定，采用varchar；

```mysql
//建表
create table t_student(
	no bigint,
    name varchar(255),
    sex char(1),
    classno varchar(255),
    birth char(10)
);

```

### INSERT语句插入数据

insert into 表名（字段名1，字段名2……） values（值1，值2……）；

删表：drop table if exists t_student;

字段可以省略不写，但是value对数量和顺序就有要求，按照建表时的顺序和 数量； 

### 表的复制

1\ 将查询结果当作表创建出来，create table 表名 as select语句  ---->  create table emp2 as select empno,ename from emp; 

2\ 将查询结果插入到一张表中 insert into dept1 select * from dept;

### 修改数据

1、update 表名 set 字段名1=值1，字段名2=值2……where 条件；  没有条件整张表数据将全部更新；

### 删除数据

delete from 表名 where 条件；        没有条件全部删除； 

delete的删表速度较慢，因为可以回滚，

不可回滚的删除：截断  truncate table emp1；

### 增删改查 crud

create、retrieve、update、delete

###  约束作用及常见约束

在创建表的时候，可以给表的字段添加相应的约束，添加约束的目的是为了保证表中数据的合法性、有效性、完整性；

非空约束not null：约束的字段不能为null

唯一约束unique：约束的字段不能重复（PK）

主键约束primary key：约束的字段不能为null，也不能重复（FK）

外键约束foreign key：

#### 非空约束

#### 唯一性约束

唯一约束修饰的字段具有唯一性，不能重复，但可以为null； 

可以字段单独唯一，也可以字段联合唯一，

```mysql
create table t_user(
	id int,
    usercode varchar(255) unique,
    username varchar(255) unique   列级约束
);
create table t_user1(
	id int ,
    usercode varchar(255),
    username varchar(255),
    unique(usercode,username)   多个字段联合添加约束，允许单列的重复
)							表级约束
```

#### 主键约束

1、表的设计，三范式，第一范式就要求任何一张表都应该有主键；

2、主键的作用：主键值是这行记录在这张表当中的唯一标识；

3、主键的分类-一张表只能有一个主键约束；

​	根据主键字段你的字段数量来划分：

​		单一主键：

​		复合主键（多个字段联合起来添加一个主键）：

​		复合主键不建议使用，因为是违背三范式的，会产生部分依赖；

​	根据主键性质来划分：

​		自然主键：和业务没有关系的自然数；

​		业务主键：主键值和 系统的业务挂钩；

​		最好不要拿着和业务挂钩的字段作为主键；因为业务的改变可能会导致主键值的改变；

4、mysql提供主键值自增；

自动维护一个自增的数字，从1开始，以1递增；

```mysql
create table t_user(
	id int primary key auto_increment,
    username varchar(255)
);
insert into t_user(unsername) value('a');
```

```mysql
create table  t_user(
	id int primary key,
    username varchar(255),
    email varchar(255)
);
```

#### 外键约束

1\ 可以起到连接两个表的作用，同时子表中的外键字段如果输入值不是父表中的主键字段，会报错，而如果不定义外键，那么就可以随便输入；

2\ t_student中的classno字段引用t_class表中的cno字段，此时t_student表叫做子表，t_class表叫做父表；删除数据的时候，先删除子表，再删除父表，添加数据的时候，先添加父表，再添加子表；

3\ 外键可以为null；

4\ 外键字段引用其他表的字段的时候，该字段不一定必须是主键，但是必须具有唯一性；

```mysql
create table t_class(
	cno int,
    cname varchar(255),
    primary key(cno)
);
create table t_student(
	sno int,
    sname varchar(255),
    classno int,
    foreign key(classno) references t_class(cno)
);
```

### 存储引擎

MySQL默认的存储引擎是InnoDB；

#### MyISAM存储引擎：

MySQL最常用的存储引擎；

用三种文件来表示表：

- 格式文件 - 存储表结构的定义mytable.frm
- 数据文件 - 存储表行的内容mytable.MYD
- 索引文件 - 存储表上索引mytable.MYI

优点：可被压缩，节省存储空间，并且可以转换为只读表，提高检索效率；

缺点：不支持事务；

#### InnoDB存储引擎

优点：支持事务、行级锁、外键等，这种存储引擎最安全；数据的安全得到保障；

该引擎下，表的结构存储在,frm文件中，，数据被存储与一个表空间tablespace中，属于一种逻辑空间，因为不能够被压缩，无法节省空间，无法转换成只读；

提供一组用来记录事务性活动的日志文件，用commit提交、savepoint及rollback回滚 来支持事务处理；

在MySQL服务器崩溃后提供自动恢复；

支持级联删除与级联更新；

### 事务

概念：一个事务是一个完整的业务逻辑单元，不可再分；（类似原子操作）；要求事务内的语句，要么同时成功，要么同时失败；（为了完成某个业务操作，多条DML（增删改）语句捆绑到一起，保证要么全部成功、要么全部失败）

和事务相关的语句只有DML语句，也就是insert、delete、update，因为这三个居于都是和数据库表中的数据相关的，事务的存在是为了保证数据的完整性、安全性；

事务的四个特征ACID:

- A 原子性；事务是最小的工作单元，不可再分；
- C 一致性：事务必须保证多条DML语句同时成功或者同时失败；
- I 隔离性：事务A与事务B之间具有隔离性；
- D 持久性：最终数据必须持久化到硬盘文件中，事务才算成功的结束；

事务的隔离级别：

- 1、读未提交（read uncommitted)
  - 对方事务还没有提交，我们当前事务可以读取到对方未提交的数据；存在脏读（dirty read）现象：表示读到了脏数据；
- 2、读提交（read committed）
  - 对方事务提交之后的数据，当前事务可以读到；解决了脏读问题，读已提交存在的问题：不可重复读；
- 3、可重复读（repeatable read）
  - 这种隔离级别解决了：不可重复读问题；存在问题：幻读；
- 4、串行
  - 解决了所有问题，但是存在效率低的问题，事务需要排队；

MySQL中默认是自动提交；所以每次事务的开始要先start transaction，然后进行DML语句，最后利用commit或者rollback来结束事务；

### 索引

1、索引就类似于一本书的目录，通过索引能够快速的找到对应的资源；

查询一张表的时候有两种检索方式：

- 1、全表扫描；
- 2、根据索引检索（效率很高，根本原理是缩小的扫描的范围）

> 索引虽然可以提高检索效率，但是不能够随意的添加索引，因为索引也是数据库当中的对象，也需要数据库不断的维护。但是有维护成本，比如表中的数据经常被修改，这样就不适合添加索引，因为数据一旦修改，索引需要重新排序，进行维护；
>
> 添加索引是对某一个字段添加；

2、加索引\删索引

```mysql
创建索引对象
create index 索引名称 on 表名（字段名）;
create index emp_sal_index on emp(sal);
删除索引对象
drop index 索引名称 on 表名；
drop index emp_sal_index on emp;
```

3、什么时候加索引

- 数据量庞大；
- 该字段很少的进行DML操作；
- 该字段经常出现在where子句中；
- 注意：主键和具有unique约束的字段会自动添加索引；所以尽量根据主键查询、检索，效率较高；

> 查看sql语句的执行计划：通过在select语句前面加上explain字段；

4、索引的底层原理

![image-20200830205041225](C:\Users\Jianlin Song\AppData\Roaming\Typora\typora-user-images\image-20200830205041225.png)

通过B Tree缩小扫描范围，底层索引进行了排序、分区，索引会携带数据在表中的‘物理地址’，最终通过索引检索到数据之后，获取到关联的物理地址，通过物理地址定位表中的数据，效率是最高的；

```mysql
select ename from emp where ename = 'SMITH';
通过索引转换为
select ename from emp where 物理地址 = 0x3；
```

索引的分类：

- 单一索引：给单个字段添加索引；
- 复合索引：给多个字段联合起来添加1个索引；
- 主键索引：主键上会自动添加索引；
- 唯一索引：有unique约束的字段上会自动添加索引；

模糊查询的时候，第一个通配符使用的是百分号%，这个时候索引是失效的；

### 视图

1、创建视图、删除视图

```mysql
create view myview as select empno,ename from emp;
drop view myview;
只有select语句可以创建视图；但是可以对视图进行增删改查；
```

2、对视图的增删改查，会影响到原表数据；

```mysql
create view myview1 as select empno,ename,sal from emp_bak;
update myview1 set ename = 'hehe',sla = 1 where empno = 7369;
delete from myview1 where empno = 7369;
```

3、视图的作用

视图可以隐藏表的实现细节，保密级别较高的系统，数据库只对外提供相关的视图，程序员只对视图对象进行crud；

### 数据库设计三范式

目的是减少数据的冗余，按照三范式设计的表不会出现数据冗余；

- 第一范式：任何一张表都应该有主键，并且每个字段原子性不可再分；
- 第二范式：建立在第一范式的基础上，所有非主键字段完全依赖主键，不能产生部分依赖；（也就是不要用复合主键）
  - 多对多？ 三张表，关系表中有两个外键；

- 第三范式：建立在第二范式的基础上，所有非主键字段直接依赖主键，不能产生传递依赖；
  - 一对多？ 两张表，代表多的那个表加外键；

在实际开发过程中，为了满足一些需求，可能会拿冗余来换取执行速度；表越多关系越复杂，减少表，增加冗余来实现更高的执行速度；（表的联查，笛卡尔积问题）

一对一的设计：

- 两种方案：
  - 主键共享，主键就是外键；
  - 外键唯一，外键+unique约束；也就是一对多的变种，在外键的基础上加了unique约束；

 比如用户登陆信息表和用户详细信息表；





### Redis的5大结构



### Redis 的 zset 的底层数据结构



### Redis 实现分布式锁









sql语句

```mysql
Student
Sid学号		 Sname学生姓名		 Sage学生年龄		Ssex学生性别
----------------------------------------------------------------------
Course
Cid课程编号		Cname课程名称		Tid教师编号
----------------------------------------------------------------------
SC
Sid学号		  Cid课程编号		 score成绩		
----------------------------------------------------------------------
Teacher 
Tid教师编号		 Tname教师名字
----------------------------------------------------------------------
1、查询“001”课程比“002”课程成绩高的所有学生的学号
select a.Sid from
(select Sid,score from SC Cid = '001') a,
(select Sid,score from SC Cid = '002') b
where a.Sid = b.Sid and a.score > b.score;
2、查询平均成绩大于60分的同学的学号和平均成绩
select Sid,avg(score) from
SC group by Sid having avg(socre) > 60;
3、查询所有同学的学号、姓名、选课数、总成绩
select s.Sid,s.Sname,count(sc.Cid),sum(score)
from Student s,SC sc
where s.Sid = sc.Sid
group by SC.Sid;
select s.Sid,s.Sname,count_cid,count_score
from Student s
left join 
(select Sid,count(Cid) as count_cid,sum(score) as count_score from SC group by Sid) as sc
on s.Sid = sc.Sid;
4、查询姓‘李’的老师的个数：
select count(Tname) from Teacher where Tname like '李%';
5、查询没有学过“叶平”老师可的同学的学号、姓名：
select Sid,Sname from


```





