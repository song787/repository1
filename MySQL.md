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

### 排序

1、order by  默认是升序；   select ename,sal, from emp order by sal;

asc 表示升序，desc表示降序；  select ename,sal from emp order by sal desc;

select ename,sal from emp order by sal desc , ename asc;  前面的优先级高，只有前面的排序字段相等的时候，会启用第二个排序规则；

select ename,sal from emp order by 2；   按照第二列排序；

2、语句的执行顺序   from -> where -> select -> order by

### 分组函数

1、 所有的分组函数都是对某一组数据进行操作的；

2、count 计数； sum 求和； avg 平均值； min max ；；； select sum(sal) from emp;

3\ 分组函数自动忽略NULL