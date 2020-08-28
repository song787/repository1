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

1\ 可以起到连接两个表的作用， 同时子表中的外键字段如果输入值不是父表中的主键字段，会报错，而如果不定义外键，那么就可以随便输入；

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

