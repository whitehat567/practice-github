

# 数据库服务器：

- 遵守国际sql标准，用来储存和管理数据的软件

- Mysql优点：mysql和Linux php，apache配合紧密；开源免费

- Postgresql：更严格遵守sql标准。

- 黑框控制台是mysql客户端，在任务管理器中是mysql.exe；
- mysql服务器，在任务管理器中是mysqld

- 其他的客户端：navcat、mysqlfront、phpmyadmin

 

入门基本语句：(都需要；)

```mysql
Mysal -uroot -ppasswd #来连接（面对的是库）
```

```mysql
tee +文件地址.sql  #功能是用来记录用户的操作记录的
set names gbk; #这是内容格式为gbk
```



# 查询模型

**列就是变量,在每一行上,列的值都在变化.**
Where条件是表达式,在哪一行上表达式为真,
哪一行就取出来
比如下面的条件, shop_price在不同的行,有不同的值.
在哪一行时,shop_price>5000如果为真,则这行取出来.
 **查询结果集--在结构上可以当成表看**



#  **库**

```mysql
Show databases;  **查看所有库**

use 库名； **查看库（面对的是表**）

create database 数据库名【charset 字符集】；  ——**创建库**

drop database 库名； ——**删除库**

#MySQL中，表、列可以改名，database不能改名。

#PhpMyAdmin中：建立新库，然后吧所有的表列复制过去。
```

 

# **表**

**关键字要大写**

```mysql
show tables;   ——**查看所有表**

use 表名； ——**查看表**

desc 表名  **——看表的详细信息**

drop table 表名; ——**删除表**

rename table表名 to 新表名； ——**表改名**
```

#### 新建表

- 先用excel列一个表，来确定列名称、类型、默认值、是否主键先确认。
- 尽量使每一个列的类型为确定的，这样方便查找。可以浪费适量的存储空间的浪费，但是提高了速度。
- 频繁有的信息，优先考虑效率，储存到一张表
- 不频繁用的信息，放到另一张辅助表之中

```mysql
create table 表名（
列1 列1类型 列1参数，
列2 列2类型 列2参数，
.....
.....
)engine myisam/innodb/bdb charset utf8/gdk/latin1....;
#engine:表存储引擎，与表性能相关		
```

#### 插入列

```mysql
#插入列
alter table 表名 add 列名 列类型 列参数 not null default 0(默认值) 

#有关键词after、before、first

alter table 表名 add 列名 列类型 列参数 not null default 0(默认值)  after 列名

#删除列：
alter table 表名 drop 列名

#修改列类型：
alter table 表名 modify  列名 新类型 新参数；

#修改列名列类型：
alter table 表名 change 旧列名 新列名  新类型 新参数； 

#注：修改类型如果不匹配，数据丢失
```



#### 插入数据

```mysql
insert into 表名 values  ——**插入数据**
(1,’zhangsan’),
(2,’lisi’),
(3,’wang’);

insert into test.goods
select goods_id,cat_id,goods_sn,goods_name,click_count,goods_number,market_price,shop_price,add_time,is_best,is_new,is_hot from shop.goods;
#从shop.goods的数据插入到test.shop;
#插入时不声明插入列名，默认为每一个列都插入。
```

#### 查看表数据

```mysql
Select * from stu;   ——**查看表的数据**
select 列名1,列名2 from 表名 where 表达式;#查看表的列1，列2中满足条件的行

#注：查看数据时，可能出现乱码，需要根据相应的环境，使用语句:set names utf8/gbk;

#黑框控制台使用的时“gbk”；mysql中字符使用的是“utf8”
```

#### 修改数据：

```mysql
update stu

set fanbu=123

where id=6;  （where后面加的是表达式，表达式为真，就改这一行）*

eg2：

update stu

where gender=’a’ and salary>200;
```



#### 清空表、删除行

```mysql
Truncate 表名； ——清空表数据

Truncate：相当于删表再重建一张相同表，操作后得到一张全新表

Delete：从删除所有的层面来操作的。
Delete from stu where 表达式；#删除整行
```

- ```mysql
  ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
  #表示主键或联合主键对应的数据已存在。
  方法：
  truncate 表名； #清空表数据。
  ```

- ```mysql
  ERROR 2006 (HY000): MySQL server has gone away
  #表示数据库断开
  方法
  重新连接数据库或者重新登陆phpMyAdmin
  ```



## 列类型

### 1.数值型

#### a.整数型

有参数，不能为空，有默认值

| tinyint   | 1字节 | 带符号：-2^7-1~2^7         | 无符号：0-2^8-1    |
| --------- | ----- | -------------------------- | ------------------ |
| smallint  | 2字节 | 带符号：-2^(2n-1)~2^(2n-1) | 无符号：0~2^(2n)-1 |
| mediumint | 3字节 |                            |                    |
| int       | 4字节 |                            |                    |
| bigint    | 8字节 |                            |                    |

参数：（M） unsigned zerofill

- （M）必须和zerofill配合使用才有意义

![1572435971758](C:\Users\daixin\AppData\Roaming\Typora\typora-user-images\1572435971758.png)

- （smallint(5)表示宽度为5）所以（M）表示补零宽度；有了zerofill默认为unsigned。
- 

#### b.浮点型

有参数，不能为零，默认值

```mysql
#M叫做“标度”——>代表“总位数”；
#D叫做“精度”——>代表”小数位“；
float(D,M), 
#存储范围：10^38,小数点精确10^(-38);M<=24,占4字节，否则占8字节
eg:float(6,2)代表:-9999.99~9999.99

decimal(D,M)
#定点型，整数部分和小数部分分开储存，比float精确。
#每个部分，九个数字打包占四个字节

mysql：
alter table add count float(6,2) not null default 0.00;
alter table add count1 decimal(6,2) not null default 0.00;

```

### 2.字符串型

```mysql
char（M）not null default '';
#定长字符串，M表示字符数,M范围0~255
#查找快（计算空间就可以找到数据）；不够长度，空格补齐，取出时，删除空格。利用率可能到100%
varchar（M）not null default '';
#变长字符串 0<=M<=65535
#储存时，会在数据前先存储数据的长度(1~2字节)，然后再储存数据。利用率i/（i+1）

alter table text add name varchar(20) not null default '';

#char(M)和varchar（M）限制的是字符数，不是字节数
#uft8中，一个字符存一个汉字，汉字占2~4字节，常用字占3字节，
#gbk中，一个字符占一个字节
text#文本，搜索速度慢，不用默认值
crate table test(artice text);
blob#二进制类型，存储图像音频等二进制信息，不用默认值
#防止因为字符集问题 导致信息丢失
create table test2(artices blob)；
```

### 3.日期/ 时间类型

无参数，不能为空，有默认值。

```mysql
creat table text（
star varchar(20) not null default '',
birthday date not null default '0000-00-00'
)engine myisam charset utf8;

```



| 类型              | 说明                   | 标准格式            | 范围                                       |
| ----------------- | ---------------------- | ------------------- | ------------------------------------------ |
| date（3字节）     | 日期                   | YYYY-MM-DD          | 1000-01-01到9999-12-31                     |
| time（3字节）     | 时间                   | HH:MM:SS            | -838:59:59和838:59:59                      |
| datetime（8字节） | 日期时间               | YYYY-MM-DD HH:MM:SS | 1000-01-01 00:00:00到9999-12-31 23：59：59 |
| year（1字节）     | 年份类型               | YYYY                | 1901到2155                                 |
| timetamp（4字节） | 取出时的时间（不更新） |                     | 提取当前时间                               |

```mysql
create table text1(
id int,
ts timetamp default CURRENT_TIMETAMP #要有这个默认值
)engine myisam charset utf8;

insert into text1(id)
values (1); 

select * from text1;

#会出现id=1的时间是调用这个select函数的时间
```



![1572440162771](C:\Users\daixin\AppData\Roaming\Typora\typora-user-images\1572440162771.png)

未声明字符集。

```mysql
set names gbk;#解决方法
```

## NULL

```mysql
建表时，加not null  default''或default 0?
答：不想让表中出现null值	
```

```mysql
为什么不要null值
答：null是一中类型，比较是只能用is null 或者 is not null，遇到其他运算符，一律返回null；
效率不高，影响提高索引效果。
```



# select 5种句子介绍

语句调用顺序：where——group或者列之间计算（结果集）——having（最终结果集）——order或limit

1. ## where 条件查询

   - 常用运算符

     | 运算符  | 说明       |
     | ------- | ---------- |
     | <       | 小于       |
     | <=      | 小于等于   |
     | =       | 等于       |
     | in      | 在某集合内 |
     | !=或<>  | 不等于     |
     | >=      | 大于等于   |
     | >       | 大于       |
     | between | 在某范围内 |

   - 逻辑运算符

     | NOT或！  | 逻辑非 |
     | -------- | ------ |
     | or或\|\| | 逻辑或 |
     | AND或&&  | 逻辑与 |

   - 模糊匹配

     - like   模糊匹配

     - %   通配任意字符

     - _   统配单一字符  //一个下划线代表一个字符

     - ```mysql
       eg: select goods_id,cat_id,goods_name from goods where goods_name like '诺基亚%';
       +----------+--------+----------------------------------------+
       | goods_id | cat_id | goods_name                             |
       +----------+--------+----------------------------------------+
       |        4 |      8 | 诺基亚N85原装充电器                    |
       |        3 |      8 | 诺基亚原装5800耳机                     |
       |        7 |      8 | 诺基亚N85原装立体声耳机HS-82           |
       |        9 |      3 | 诺基亚E66                              |
       |       13 |      3 | 诺基亚5320 XpressMusic                 |
       |       14 |      4 | 诺基亚5800XM                           |
       |       23 |      5 | 诺基亚N96                              |
       |       32 |      3 | 诺基亚N85                              |
       +----------+--------+----------------------------------------+
       
       select goods_id,cat_id,goods_name from goods where goods_name like '诺基亚N__';
       +----------+--------+--------------+
       | goods_id | cat_id | goods_name   |
       +----------+--------+--------------+
       |       23 |      5 | 诺基亚N96    |
       |       32 |      3 | 诺基亚N85    |
       +----------+--------+--------------+
       ```

   - 列可以看作变量，可以进行运算

     例题1：
     
     ```mysql
     有如下表和数组
     把num值处于[20,29]之间,改为20
     num值处于[30,39]之间的,改为30
     
     test1表
     +------+
     | num  |
     +------+
     |    3 |
|   12 |
     |   15 |
|   25 |
     |   23 |
     |   29 |
     |   34 |
     |   37 |
     |   32 |
     |   45 |
     |   48 |
     |   52 |
     +------+
     语句：
     update test1 set num=floor(num/10)*10 where num>=20 and num<=39;
     
     #floor 取整函数
     ```
     
     ```mysql
     把good表中商品名为'诺基亚xxxx'的商品,改为'HTCxxxx',
     提示:大胆的把列看成变量,参与运算,甚至调用函数来处理 .
     #substring(列名，第几位),截取函数
     #concat(字符（或者列名），列名) 连接函数
     
     select goods_name,concat('HTC',substring(goods_name,4)) from goods where goods_name like'诺基亚%';
     +----------------------------------------+---------------------------------------+
     | goods_name                             | concat('HTC',substring(goods_name,4)) |
     +----------------------------------------+---------------------------------------+
     | 诺基亚N85原装充电器                    | HTCN85原装充电器                      |
     | 诺基亚原装5800耳机                     | HTC原装5800耳机                       |
| 诺基亚N85原装立体声耳机HS-82           | HTCN85原装立体声耳机HS-82             |
     | 诺基亚E66                              | HTCE66                                |
     | 诺基亚5320 XpressMusic                 | HTC5320 XpressMusic                   |
     | 诺基亚5800XM                           | HTC5800XM                             |
     | 诺基亚N96                              | HTCN96                                |
     | 诺基亚N85                              | HTCN85                                |
     +----------------------------------------+---------------------------------------+
     ```
     
     
   
2. ## group by 分组

   - 聚合函数

     - | 函数名 | 说明                                                         |
       | ------ | ------------------------------------------------------------ |
       | max    | 最大（括号内可做逻辑运算，若是真为1，假为0，比较的是1或0）   |
       | min    | 最小（括号内可做逻辑运算，若是真为1，假为0，比较的是1或0）   |
       | sum    | 求总和（括号内可做逻辑运算，若是真为1，假为0，然后求和）     |
       | avg    | 求平均（括号内可作逻辑运算，若是真为1，假为0，然后求和，再除以总行数） |
       | count  | 求总行数(括号内不做逻辑运算)                                 |

     - ```mysql
       eg：
       select max(shop_price) from goods; 
       #求shop_price列最大值
       select sum(goods_number) from goods;
       #求goods_number列和
       select count(*或者1) from goods;
       #查询绝对行数
       select count(列名) from goods;
       #查询该列名部位null的值
       ```

   - group介绍

     - ```mysql
       group by
       作用:把行 按 字段 分组
       语法:group by col1,col2,...colN
       运用场合：常见于统计场合,如按栏目计算帖子数,统计每个人的平均成绩等.
       select 列1,统计函数 from 表名 group by 列1
   #查询的列必须和分组列相同。
       ```
       
     - ```mysql
       eg：
       select sum(goods_number) from goods group by cat_id;
     #按照cat_id分组后，求每组的goods_number的和；
       ```
     
       

3. ## having 筛选

   - ```mysql
     select 列1,列2....列n as 新列名x from 表名 where 1 having 新列名x; 
     #where先进行查询，对象是原来的表
     #having是对查询后的结果进行查询，对象是查询后的结果。
      
     select goods_id,goods_name,(market_price-shop_price) as discount from goods where (market_price-shop_price) ;
     
     select goods_id,goods_name,(market_price-shop_price) as discount from goods having discount >200;
     
     ```

   - ```mysql
     例题：
     有如下表及数据
     +------+---------+-------+
     | name | subject | score |
     +------+---------+-------+
     | 张三 | 数学    |    90 |
     | 张三 | 语文    |    50 |
     | 张三 | 地理    |    40 |
     | 李四 | 语文    |    55 |
     | 李四 | 政治    |    45 |
     | 王五 | 政治    |    30 |
     +------+---------+-------+
     
     要求:查询出2门及2门以上不及格者的平均成绩
     
     错误：
     select name,avg(score) from test3 where score<60 groupby name;
     #没有计算到张三 数学 90 这一行
     
     select name,avg(score) from test3 group by name having count(score<60)>=2
     #count(score<60)中count不会管其括号的内容逻辑，不论是0还是1，结果一样的
     
     正解：
     select name,avg(score) from test3 group by name;
     #查出所有人的平均成绩
     select name,subject,score,score<60 as g from test3;
     #把每个科目挂科了作为1，没挂科作为0
     select name,avg(score),sum(score<60) as gks from test3 group by name;
     #按名字分组，统计出平均分和每个人的挂科数目
     select name,avg(score),sum(score<60) as gks from test3 group by name having gks>=2;
     #得出结果
     ```

   

4. ## order by 排序

   - ```mysql
     order by 列名1 desc（降序）/asc(升序，默认是升序)，列名2 desc（降序）/asc(升序，默认是升序).。。。 
     
     select goods_id,goods_name,shop_price from goods where cat_id=4 order by shop_price asc;
     #按照shop_price升序排列。 
     select cat_id goods_id,goods_name,shop_price from goods order by cat_id,shop_price desc;
     #按照栏目升序（优先级高），价格降序
     ```

     

5. ## limit 限制结束条数

   - ```mysql
     Limit 限制条数
     limit [offset,] N,限制结果取N条
     用法: 
     limit [偏移量,]（跳过几行；若offset为0，可以不写）,
     N：取出条目。
     知识点的运用场合描述：
     分页应用中最为典型,如第1页取1-20条,第2页取21-40条.
     
     select cat_id goods_id,goods_name,shop_price from goods where cat_id=3 order by shop_price desc limit 10;
     #取价格前10高的
     select cat_id goods_id,goods_name,shop_price from goods where cat_id=3 order by shop_price desc limit 2,3;
     #取价格第三到第五的商品
     ```

   

   

# 子查询

## where型子查询

```mysql
把内层查询的结果作为外层的查询比较条件
```

例题：

```mysql
#查出本站最新的（goods_id最大）的一条商品
select goods_id,goods_name from goods order by goods_id desc limit 1;

select max(goods_id) from goods;
select goods_id,goods_name from goods where goods_id=33;

#把查询语句的返回值作为比较条件
select goods_id,goods_name from goods where goods_id=(select max(goods_id) from goods);
```

例题：

```mysql
#每个栏目下goods_id最大的商品
1.select max(goods_id) from goods group by cat_id; 
2.select goods_id,goods_name,cat_id from goods where goods_id in (select max(goods_id) from goods group by cat_id);
```

## from型子查询

```
内层sql查询结果，当成（as）一张临时表，供外层sql再次查询
```

例题：

```mysql
#每个栏目下goods_id最大的商品
1.select goods_id,cat_id,goods_name from goods order by cat_id asc,goods_id desc;
2.select * from (select goods_id,cat_id,goods_name from goods order by cat_id asc,goods_id desc) as tmp group by cat_id;
#注意：一定要将内层sql查询结果 as 表名（临时）；
```

## exists型子查询

```
把外层sql的结果，拿到内层sql去测试
如果内层sql成立，则该行取出
```

例题

```mysql
#查出有商品的栏目
select cat_id,cat_name from cateory where exists(select * from goods where goods.cat_id=cateory.cat_id);

理解：
test.cateory
+--------+---------------------------+
| cat_id | cat_name                  |
+--------+---------------------------+
|      1 | 手机类型                  |
|      2 | CDMA手机                  |
|      3 | GSM手机                   |
|      4 | 3G手机                    |
|      5 | 双模手机                  |
|      6 | 手机配件                  |
|      7 | 充电器                    |
|      8 | 耳机                      |
|      9 | 电池                      |
|     11 | 读卡器和内存卡            |
|     12 | 充值卡                    |
|     13 | 小灵通/固话充值卡         |
|     14 | 移动手机充值卡            |
|     15 | 联通手机充值卡            |
+--------+---------------------------+
假设select cat_id,cat_name from cateory的第一行成立
即cateory.cat_id=1;
把cateory.cat_id=1带入到内层sql语句
select * from goods where goods.cat_id=1；
如果这语句有值，则exists成立，则cateory.cat_id=1成立，可以取出来；
如果这语句为空，则exists不成立，则cateory.cat_id=1不成立，不可以取出来；
```



# 连接查询

```1
集合：无序性，唯一性
集合的运算：求交集，求并集，笛卡尔积（集合相乘）
笛卡尔积（集合相乘）:集合A(1,2,3)；集合B（2，4）
	A*B	=		:{(1,2),(1,4),(2,2),(2,4),(3,2),(3,4)}
```

```
每一张表就是一个集合
每一行就是一个元素

两表做全相乘：
从行：两表的行两两组合；
从列：两表的列相加。
```

```mysql
select * from 表1,表2;  #相当于表1*表2
select * from 表1,表2 where 条件 ;
```



## 左连接

##### 尽量用左连接，出于移植时兼容性方面的考虑。

```mysql
左连接语法：
假设A表在左，不动；B表在A表的右边滑动
A表与B表通过一个关系来筛选B表的行

A left join B on 条件;  
#条件为真，则B表对应行取出
#“A left join B on 条件;”这个可以作为结果集，可以看作一个表，就可以多次使用左连接，只要注意同一张表多次出现时，采用取别名的方式，让表能够被识别。
#以A表为基准，A在B中没有配对时，用NULL补齐
```

```mysql
主持人：所有男生上台，带上自己配偶；
库：test；表：boy；girl
select boy.*,girl.* from boy left join girl on boy.other=girl.other;
+-----------+-------+-----------+-------+
| bname     | other | gname     | other |
+-----------+-------+-----------+-------+
| 李四      | B     | 空姐      | B     |
| 王五      | C     | 大S       | C     |
| 高富帅    | D     | 张柏芝    | D     |
| 高富帅    | D     | 阿娇      | D     |
| 郑七      | E     | 林黛玉    | E     |
| 屌丝      | A     | NULL      | NULL  |
+-----------+-------+-----------+-------+
```



## 右连接

```mysql
A right join B on 条件;
#等价于 B left join A on 条件;

```

```mysql
主持人：所有女生上台，带上自己配偶；
库：test；表：boy；girl
select boy.*,girl.* from boy right join girl on boy.other=girl.other;
+-----------+-------+-----------+-------+
| bname     | other | gname     | other |
+-----------+-------+-----------+-------+
| 李四      | B     | 空姐      | B     |
| 王五      | C     | 大S       | C     |
| 高富帅    | D     | 张柏芝    | D     |
| 高富帅    | D     | 阿娇      | D     |
| 郑七      | E     | 林黛玉    | E     |
| NULL      | NULL  | 宝钗      | F     |
+-----------+-------+-----------+-------+
```



## 内连接

```mysql
A inner join B on 条件;
#不会出现NULL补齐，只有满足条件才会被取出。
#即是左右连接的交集	
```

```mysql
主持人：所有有配偶的男生、女生上台；
库：test；表：boy；girl
select boy.*,girl.* from boy inner join girl on boy.other=girl.other;
+-----------+-------+-----------+-------+
| bname     | other | gname     | other |
+-----------+-------+-----------+-------+
| 李四      | B     | 空姐      | B     |
| 王五      | C     | 大S       | C     |
| 高富帅    | D     | 张柏芝    | D     |
| 高富帅    | D     | 阿娇      | D     |
| 郑七      | E     | 林黛玉    | E     |
+-----------+-------+-----------+-------+
```

## union 

```mysql
合并2条或多条语句的结果
即合并的是结果集
由于没有储存，所有不涉及到类型的转换
sql1 union sql2

#两个结果集的列名不一样不一样发生什么？
#答：以第一个sql语句的结果列名为准。

#union满足什么条件可以用？
#答:sql语句的结果集的列数量一致就可以使用。（列的类型不同也可以合并，只是没有意义）

#sql1中有order by 语句时，当order by 不影响结果集，仅仅是排序，则不发挥作用,被mysql优化掉；当order by 影响结果集时，会发挥作用（如：order by 列名 limit 3;order by 会有作用）

#union后的结果有重复（即某2行或N行所有列，值都一样），怎么办？
#答：默认是去除重复的。

#想不去重复
#答：使用 union all
```







例题

```mysql

根据给出的表结构按要求写出SQL语句。
Match 赛程表
字段名称	字段类型	描述
matchID	int	主键
hostTeamID	int	主队的ID
guestTeamID	int	客队的ID
matchResult	varchar(20)	比赛结果，如（2:0）
matchTime	date	比赛开始时间


Team 参赛队伍表
字段名称	字段类型	描述
teamID	int	主键
teamName	varchar(20)	队伍名称


Match的hostTeamID与guestTeamID都与Team中的teamID关联
查出 2006-6-1 到2006-7-1之间举行的所有比赛，并且用以下形式列出：
拜仁  2：0 不来梅 2006-6-21

解答：
select t1.tname,mres,t2.tname,matime from m left join t as t1 on m.hid=t1.tid left join t as t2 on m.gid=t2.tid where matime between '2006-06-01' and '2006-07-01';
+--------------+-------+--------------+------------+
| tname        | mres  | tname        | matime     |
+--------------+-------+--------------+------------+
| 公益联队     | 2：5  | 国花         | 2006-06-25 |
| 申花         | 1：2  | 公益联队     | 2006-06-21 |
+--------------+-------+--------------+------------+
#注意：
使用两次左连接时，会导致表出现多次，让系统无法区分，采取取别名的方式解决
表名 as 表别名
```

# 函数

## 一、数学函数

```mysql
abs(x)   		#返回x的绝对值
bin(x)   		#返回x的二进制（oct返回八进制，hex返回十六进制）
ceiling(x)  	#返回大于x的最小整数值
exp(x)   		#返回值e（自然对数的底）的x次方
floor(x)   		#返回小于x的最大整数值
greatest(x1,x2,...,xn)#返回集合中最大的值
least(x1,x2,...,xn)      #返回集合中最小的值
ln(x)                    #返回x的自然对数
log(x,y)		#返回x的以y为底的对数
mod(x,y)        #返回x/y的模（余数）
pi()			#返回pi的值（圆周率）
rand()			#返回０到１内的随机值,可以通过提供一个参数(种子)使rand()随机数生成器生成一个指定的值。
round(x,y)		#返回参数x的四舍五入的有y位小数的值
sign(x) 		#返回代表数字x的符号的值
sqrt(x) 		#返回一个数的平方根
truncate(x,y)   #返回数字x截短为y位小数的结果
```

## 二、聚合函数(常用于group by从句的select查询中)

```mysql
avg(col)			#返回指定列的平均值
count(col)			#返回指定列中非null值的个数
min(col)			#返回指定列的最小值
max(col)			#返回指定列的最大值
sum(col)			#返回指定列的所有值之和
group_concat(col)	#返回由属于一组的列值连接组合而成的结果
```

## 三、字符串函数

```mysql
ascii(char)			#返回字符的ascii码值
bit_length(str)		#返回字符串的比特长度
concat(s1,s2...,sn)	#将s1,s2...,sn连接成字符串
concat_ws(sep,s1,s2...,sn)#将s1,s2...,sn连接成字符串，并用sep字符间隔
insert(str,x,y,instr) 	  #将字符串str从第x位置开始，y个字符长的子串替换为字符串instr，返回结果
find_in_set(str,list)	  #分析逗号分隔的list列表，如果发现str，返回str在list中的位置
lcase(str)或lower(str)    #返回将字符串str中所有字符改变为小写后的结果
left(str,x)				  #返回字符串str中最左边的x个字符
length(s)				 #返回字符串str中的字符数
ltrim(str) 				#从字符串str中切掉开头的空格
position(substr in str)	#返回子串substr在字符串str中第一次出现的位置
quote(str) 				#用反斜杠转义str中的单引号
repeat(str,srchstr,rplcstr)#返回字符串str重复x次的结果
reverse(str) 			#返回颠倒字符串str的结果
right(str,x) 			#返回字符串str中最右边的x个字符
rtrim(str) 				#返回字符串str尾部的空格
strcmp(s1,s2)			#比较字符串s1和s2
trim(str)				#去除字符串首部和尾部的所有空格
ucase(str)或upper(str) 	#返回将字符串str中所有字符转变为大写后的结果

```

## 四、日期和时间函数

```mysql
curdate()或current_date() #返回当前的日期
curtime()或current_time() #返回当前的时间
date_add(date,interval int keyword)#返回日期date加上间隔时间int的结果(int必须按照关键字进行格式化),如：selectdate_add(current_date,interval 6 month);
date_format(date,fmt)  #依照指定的fmt格式格式化日期date值
date_sub(date,interval int keyword)#返回日期date加上间隔时间int的结果(int必须按照关键字进行格式化),如：selectdate_sub(current_date,interval 6 month);
dayofweek(date)   #返回date所代表的一星期中的第几天(1~7)
dayofmonth(date)  #返回date是一个月的第几天(1~31)
dayofyear(date)   #返回date是一年的第几天(1~366)
dayname(date)     #返回date的星期名，如：select dayname(current_date);
from_unixtime(ts,fmt)  #根据指定的fmt格式，格式化unix时间戳ts
hour(time)             #返回time的小时值(0~23)
minute(time)   		   #返回time的分钟值(0~59)
month(date)   		   #返回date的月份值(1~12)
monthname(date)        #返回date的月份名，如：select monthname(current_date);
now()    				#返回当前的日期和时间
quarter(date)    		#返回date在一年中的季度(1~4)，如select quarter(current_date);
week(date)   			#返回日期date为一年中第几周(0~53)
year(date)   			#返回日期date的年份(1000~9999)
一些示例：
获取当前系统时间：select from_unixtime(unix_timestamp());
select extract(year_month from current_date);
select extract(day_second from current_date);
select extract(hour_minute from current_date);
返回两个日期值之间的差值(月数)：select period_diff(200302,199802);
在mysql中计算年龄：
select date_format(from_days(to_days(now())-to_days(birthday)),'%y')+0 as age from employee;
这样，如果brithday是未来的年月日的话，计算结果为0。
下面的sql语句计算员工的绝对年龄，即当birthday是未来的日期时，将得到负值。
select date_format(now(), '%y') - date_format(birthday, '%y') -(date_format(now(), '00-%m-%d') <date_format(birthday, '00-%m-%d')) as age from employee
```

## 五、加密函数

```mysql
aes_encrypt(str,key)  #返回用密钥key对字符串str利用高级加密标准算法加密后的结果，调用aes_encrypt的结果是一个二进制字符串，以blob类型存储
aes_decrypt(str,key)  #返回用密钥key对字符串str利用高级加密标准算法解密后的结果
decode(str,key)   #使用key作为密钥解密加密字符串str
encrypt(str,salt)   #使用unixcrypt()函数，用关键词salt(一个可以惟一确定口令的字符串，就像钥匙一样)加密字符串str
encode(str,key)   #使用key作为密钥加密字符串str，调用encode()的结果是一个二进制字符串，它以blob类型存储
md5()    #计算字符串str的md5校验和，不可逆。
password(str)   #返回字符串str的加密版本，这个加密过程是不可逆转的，和unix密码加密过程使用不同的算法。
sha()    #计算字符串str的安全散列算法(sha)校验和
示例：
select encrypt('root','salt');
select encode('xufeng','key');
select decode(encode('xufeng','key'),'key');#加解密放在一起
select aes_encrypt('root','key');
select aes_decrypt(aes_encrypt('root','key'),'key');
select md5('123456');
select sha('123456');
```

## 六、控制流函数

```mysql
mysql有4个函数是用来进行条件操作的，这些函数可以实现sql的条件逻辑，允许开发者将一些应用程序业务逻辑转换到数据库后台。
mysql控制流函数：
case when[test1] then [result1]...else [default] end如果testn是真，则返回resultn，否则返回default

case [test] when[val1] then [result]...else [default]end  如果test和valn相等，则返回resultn，否则返回default

if(test,t,f)   如果test是真，返回t；否则返回f

ifnull(arg1,arg2) 如果arg1不是空，返回arg1，否则返回arg2

nullif(arg1,arg2) 如果arg1=arg2返回null；否则返回arg1
#这些函数的第一个是ifnull()，它有两个参数，并且对第一个参数进行判断。如果第一个参数不是null，函数就会向调用者返回第一个参数；如果是null,将返回第二个参数。
如：select ifnull(1,2), ifnull(null,10),ifnull(4*null,'false');
nullif()函数将会检验提供的两个参数是否相等，如果相等，则返回null，如果不相等，就返回第一个参数。
如：select nullif(1,1),nullif('a','b'),nullif(2+3,4+1);
和许多脚本语言提供的if()函数一样，mysql的if()函数也可以建立一个简单的条件测试，这个函数有三个参数，第一个是要被判断的表达式，如果表达式为真，if()将会返回第二个参数，如果为假，if()将会返回第三个参数。
如：selectif(1<10,2,3),if(56>100,'true','false');
if()函数在只有两种可能结果时才适合使用。然而，在现实世界中，我们可能发现在条件测试中会需要多个分支。在这种情况下，mysql提供了case函数，它和php及perl语言的switch-case条件例程一样。
case函数的格式有些复杂，通常如下所示：
case [expression to be evaluated]
when [val 1] then [result 1]
when [val 2] then [result 2]
when [val 3] then [result 3]
......
when [val n] then [result n]
else [default result]
end
这里，第一个参数是要被判断的值或表达式，接下来的是一系列的when-then块，每一块的第一个参数指定要比较的值，如果为真，就返回结果。所有的when-then块将以else块结束，当end结束了所有外部的case块时，如果前面的每一个块都不匹配就会返回else块指定的默认结果。如果没有指定else块，而且所有的when-then比较都不是真，mysql将会返回null。
case函数还有另外一种句法，有时使用起来非常方便，如下：
case
when [conditional test 1] then [result 1]
when [conditional test 2] then [result 2]
else [default result]
end
这种条件下，返回的结果取决于相应的条件测试是否为真。
示例：
mysql>select case 'green'
     when 'red' then 'stop'
     when 'green' then 'go' end;
select case 9 when 1 then 'a' when 2 then 'b' else 'n/a' end;
select case when (2+2)=4 then 'ok' when(2+2)<>4 then 'not ok' end asstatus;
select name,if((isactive = 1),'已激活','未激活') as result fromuserlogininfo;
select fname,lname,(math+sci+lit) as total,
case when (math+sci+lit) < 50 then 'd'
when (math+sci+lit) between 50 and 150 then 'c'
when (math+sci+lit) between 151 and 250 then 'b'
else 'a' end
as grade from marks;
select if(encrypt('sue','ts')=upass,'allow','deny') as loginresultfrom users where uname = 'sue';#一个登陆验证
```

## 七、格式化函数

```mysql
date_format(date,fmt)  #依照字符串fmt格式化日期date值
format(x,y)   #把x格式化为以逗号隔开的数字序列，y是结果的小数位数
inet_aton(ip)   #返回ip地址的数字表示
inet_ntoa(num)   #返回数字所代表的ip地址
time_format(time,fmt)  #依照字符串fmt格式化时间time值
其中最简单的是format()函数，它可以把大的数值格式化为以逗号间隔的易读的序列。
示例：
select format(34234.34323432,3);
select date_format(now(),'%w,%d %m %y %r');
select date_format(now(),'%y-%m-%d');
select date_format(19990330,'%y-%m-%d');
select date_format(now(),'%h:%i %p');
select inet_aton('10.122.89.47');
select inet_ntoa(175790383);

```

## 八、类型转化函数

```mysql
#为了进行数据类型转化，mysql提供了cast()函数，它可以把一个值转化为指定的数据类型。类型有：binary,char,date,time,datetime,signed,unsigned
示例：
select cast(now() as signed integer),curdate()+0;
select 'f'=binary 'f','f'=cast('f' as binary);
```

## 九、系统信息函数

```mysql
database()   #返回当前数据库名
benchmark(count,expr)  #将表达式expr重复运行count次
connection_id()   #返回当前客户的连接id
found_rows()   #返回最后一个select查询进行检索的总行数
user()或system_user()  #返回当前登陆用户名
version()   #返回mysql服务器的版本
```

## 函数注意事项

```mysql
如果mysql函数和PHP函数都实现某个功能，优先哪一个？

1.mysql的函数肯定是要影响查询速度，应该在建表时，通过合理的表结构减少函数的使用；比如：email，按@前后拆分。

2.如果确实要用函数，比如：时间的格式化，在mysql里面date_format，在PHP里可以用date可以实现，优先放在业务逻辑层，即PHP层处理。

3.在查询时使用了函数，最大的坏处
以date_formate(a)，则A列的索引将无法使用
如果你针对某列查询，而此列用上了函数来判断，此列将不再使用索引，
如：selecet name,email from table where right(position(email))='qq.com';
emali 列是有索引的，可以加快查询速度，
但因为使用的并不是email列，而是函数处理后的email的返回值，查询就很慢。
```

# 视图 view

**VIEW可以看作一张临时的表，是表通过某种运算得到的一个投影（会根据原来表的变化而变化）**

```mysql
create view 视图名 as select 语句;
```

```
1.表与视图的影响关系
表的数据变化，会影响视图的数据
2.视图某种情况下，也可以修改的
要求：视图的数据和表的关系一一对应，就像函数的映射
表-->推出视图的对应数据
视图-->推出表的数据
注意的是：视图的数据修改，对于表中的数据一定是确定的修改对应一条，不能摸棱两可。
一一对应：根据select关系，从表中取出的行，只能计算出视图中确定的一行

3.视图只有结构，不占空间。
```

```
作用：
1.简化我们的查询，比如复杂的统计时，先用视图生成一个中间结果，再查询视图。
2.更精细的权限控制
3.数据多，分表可以用到
```

```mysql
algorithm=
merge     合并语句
eg：
select goods_nember,shop_price where shop_price >300;
select goods_nember,shop_price where shop_price <500;
#这两个就可以合并
select * from goods order by cat_id asc,shop_price desc;
select *from goods group by cat_id;
#这两个就无法合并
temptable 临时表
undefined 未定义，由系统判断
```

# mysql字符集详解

- ANSI：本地编码；GBK：中文编码；UTF-8：国际编码
  
- 乱码的形成
  
  - 解码与实际编码不一致
- 传输过程中编码不一致，导致字节丢失。
  
- 客户端(GBK)-->连接器(UTF8)-->服务器(UTF8)

  - 三个参数：客服端的发送编码，连接器使用的编码，获得返回数据的编码

  - ```mysql
    set character_set_client=gbk;#客服端的发送编码
    set character_set_connection=utf8;#连接器使用的编码
    set character_set_results=gbk;#获得返回数据的编码
    
    如果三个参数都是gbk
    可以简写：set name gbk;
    #服务器&gt;=connection&gt;=客户端	
    ```

  - 小结：
  
    - 正确指定客户端的编码
    - 合理选择连接器的编码
    - 正确指定返回内容的编码
    - 网页文件本身的编码，meta信息（html的标签，表示网页文档的属性）

- bom信息：用来表示这是那种编码，文件表示utf-8+表示有哦bom头。
- utf8和UTF-8
  - UTF-8是一种编码，世界只有一种；mysql种utf8就是UTF-8.

- 例题：

  - ```
    uft-8中文截取无乱码
    思路：要知道截取那几位。
    ```

# 存储引擎engine

- engine是mysql存储数据的不同方式

![mysql存储引擎](C:\Users\daixin\Pictures\mysql存储引擎.png)

- 事务

  ```
  -----
  多人买火车票
  两个人几乎同时买票，票只有一张
  张三看：有一张票
  李四看：有一张票
  -----
  银行转账：
  张三给李四转500
  张三-500
  李四+500
  只有这两步都完成，才算转账完成。
  -----
  像这种，，2步或者N步，从逻辑讲，是一个“原子操作”
  即：要么成功，要么都不成功
  为了保证这种特性：用事务。
  ```

  特性：

  - 原子性：要么成功，要么都不成功

  - 一致性：操作前后，值的变化，逻辑上成立

    - ```
      张三-500，李四+300，这是不成立的。
      ```

      

  - 隔离性：事务结束前，每一步的操作带来的影响别的会话看不见

    - ```
      a到网点存钱，往某账号打3W
      看到工作人员操作后，还没最终确认（但此时，数据库已经加上）
      B，再ATM前抓紧取出
      A再说：不存了
      这就没有用到隔离性。
      ```

      

  - 持久性：事务一旦完成，无法撤销

    - ```
      ATM取钱
      账户-500
      吐钞500，钱装兜里
      这时出错了，如何撤销这个事务？
      事务不能撤销，但确实时一次错误的交易，怎么办？
      只能，再做一次“补偿式事务”
      ```

  - 语法：

    ```mysql
    start transaction;# 开启事务，每次commit或者rollback后都要开启事务
    commit;#提交事务，表示事务结束了
    rollback;#当有一个事件失败时，其他成功的事件要回滚，操作后表示事务结束 
    ```

    