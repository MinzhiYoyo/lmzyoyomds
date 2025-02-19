# MySQL

MySQL 是一个广泛使用的关系型数据库管理系统（DBMS），用于存储和管理电子数据。MySQL使用表格来组织数据，每个表格包含多个记录（行）和字段（列）。这种结构使得数据易于管理和查询。MySQL可以管理多个数据库，每个数据库中会含有多张表。

另外，推荐MySQL的关键字用大写（虽然不区分大小写），但是英语水平不好，小写更容易记住一点，所以接下来一般都用小写。

下面简单安装一下：以ubuntu为例（可以手动下载 `MySQL Workbench`可视化工具）

```shell
sudo apt update
sudo apt install mysql-server
sudo systemctl start mysql
sudo systemctl enable mysql
sudo mysql -p # 以root用户登录mysql，默认没有密码
```

下面设置`root`密码：

```mysql
> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '密码'; # 下次登录就需要这个密码了
> flush privileges;  # 刷新缓存
```

重新登录：

```mysql
sudo mysql -uroot -p
```

创建用户：先登录`root`

1.   创建用户

```mysql
# username是用户名
# host是登录IP，为'%'表示任意IP，为'localhost'表示本机
# password为密码
CREATE user 'username'@'host' identified by 'password'; 
```

2.   授权

```mysql
# *.*是（数据库.表），就是授予哪个数据库和哪个表，*表示所有数据库/表
# % 表示登录的IP
grant all privileges on *.* to 'username'@'%' with grant option;
```

3.   刷新权限

```mysql
flush privileges;
```

4.   撤销授权

```mysql
REVOKE ALL PRIVILEGES ON *.* FROM user_name; # 收回权限（不包含赋权权限）
REVOKE GRANT OPTION ON *.* FROM user_name; # 收回赋权权限
flush privileges; # 刷新权限
```

# MySQL的操作

这一部分的内容只在`MySQL`上面有，不是`SQL`的内容。

`information_schema`, `mysql`, `performance_schema`和`sys`是系统的库，不用去管。

## 创建数据库

创建数据库有下面的方式：

-   登录数据库创建

```mysql
create database [if not exists] database_name # database_name为数据库名字，不加 if not exists 就不会检测是否存在同名数据库，存在的话创建失败
character set character_set # 数据集，一般是 utf8mb4
collate collation_name; # 校对规则，一般是 utf8mb4_general_ci
```

-   使用`mysqladmin`创建，在`shell`就行，不用登录

```shell
mysqladmin -u your_username -p create your_database \
--default-character-set=utf8mb4 \
--default-collation=utf8mb4_general_ci
```

在我们创建数据库之后，登录`mysql`

```mysql
show databases; # 显示有哪些数据库
use database_name_; # 使用哪个数据库

show tables; # 显示当前数据库有哪些表
use table_name_; # 使用哪张表
```

直接使用数据库可以：

```shell
mysql -u your_username -p -D your_database
```

## 删除数据库

-   登录

```mysql
drop database [if exists] database_name;
```

-   使用`shell`

```shell
mysqladmin -u your_username -p drop your_database
```

## 数据类型

`mysql`支持大部分`sql`的数据类型

### 1）数值类型

|     类型     |          大小(字节)           |                        范围（有符号）                        |                        范围（无符号）                        |      用途       |
| :----------: | :---------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :-------------: |
|   TINYINT    |               1               |                        （-128， 127）                        |                          （0，255）                          |    大整数值     |
|   SMALLINT   |               2               |                      （-32768， 32767）                      |                         （0，65535）                         |    大整数值     |
|  MEDIUMINT   |               3               |                    （-8388608，8388607）                     |                       （0，16777215）                        |    大整数值     |
| INT/INTEGER  |               4               |                 （-2147483648，2147483647）                  |                      （0，4294967295）                       |    大整数值     |
|    BIGINT    |               8               |        （-9223372036854775808，9223372036854775807）         |                 （0，18446744073709551615）                  |    大整数值     |
|    FLOAT     |            4 Bytes            | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) |         0，(1.175 494 351 E-38，3.402 823 466 E+38)          | 单精度 浮点数值 |
|    DOUBLE    |            8 Bytes            | (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 双精度 浮点数值 |
| DOUBLE(M,D)  | 8，（M代表长度，D代表小数位） |        同上，受M,D的约束DOUBLE(5,2)-999.999,+999.999         |                      同上，受M，D的约束                      | 双精度浮点数值  |
| DECIMAL(M,D) |         DECIMAL(M,D)          |                   依赖于M,D的值，M最大值65                   |                                                              |     小数值      |

### 2）日期类型

|   类型    | 大小 |                             范围                             |        格式         |           用途           |
| :-------: | :--: | :----------------------------------------------------------: | :-----------------: | :----------------------: |
|   DATE    |  3   |                    1000-01-01/9999-12-31                     |     YYYY-MM-DD      |          日期值          |
|   TIME    |  3   |                   '-838:59:59'/'838:59:59'                   |      HH:MM:SS       |     时间值或持续时间     |
|   YEAR    |  1   |                          1901/2155                           |        YYYY         |          年份值          |
| DATETIME  |  8   |        '1000-01-01 00:00:00' 到 '9999-12-31 23:59:59'        | YYYY-MM-DD hh:mm:ss |     混合日期和时间值     |
| TIMESTAMP |  4   | '1970-01-01 00:00:01' UTC 到 '2038-01-19 03:14:07' UTC结束时间是第 **2147483647** 秒，北京时间 **2038-1-19 11:14:07**，格林尼治时间 2038年1月19日 凌晨 03:14:07 | YYYY-MM-DD hh:mm:ss | 混合日期和时间值，时间戳 |

### 3）字符串

|    类型    |         大小          |              用途               |
| :--------: | :-------------------: | :-----------------------------: |
|    CHAR    |      0-255 bytes      |           定长字符串            |
|  VARCHAR   |     0-65535 bytes     |           变长字符串            |
|  TINYBLOB  |      0-255 bytes      | 不超过 255 个字符的二进制字符串 |
|  TINYTEXT  |      0-255 bytes      |          短文本字符串           |
|    BLOB    |    0-65 535 bytes     |     二进制形式的长文本数据      |
|    TEXT    |    0-65 535 bytes     |           长文本数据            |
| MEDIUMBLOB |  0-16 777 215 bytes   |  二进制形式的中等长度文本数据   |
| MEDIUMTEXT |  0-16 777 215 bytes   |        中等长度文本数据         |
|  LONGBLOB  | 0-4 294 967 295 bytes |    二进制形式的极大文本数据     |
|  LONGTEXT  | 0-4 294 967 295 bytes |          极大文本数据           |

### 4) 枚举与集合类型（Enumeration and Set Types）

-   **ENUM**: 枚举类型，用于存储单一值，可以选择一个预定义的集合。
-   **SET**: 集合类型，用于存储多个值，可以选择多个预定义的集合。

### 5) 空间数据类型（Spatial Data Types）

GEOMETRY, POINT, LINESTRING, POLYGON, MULTIPOINT, MULTILINESTRING, MULTIPOLYGON, GEOMETRYCOLLECTION: 用于存储空间数据（地理信息、几何图形等）。

## 创建表

```mysql
create table [if not exists] `table_name` (  # 这里的反引号作用就是防止名字中出现 mysql 不允许的字符，也可以理解成字符串
    `col_name1` int unsigned auto_increment # auto_increment 表示自动增加
    `col_name2` varchar(100) not NULL,      # not null表示不能为null
    `col_name3` varchar(30) NULL,           # 表示可以为null
    `col_name4` boolean default true,        # 表示默认为true       
    `col_name5` date,                        # 创建日期类型的
    ...
    primary key (`col_namex`)  # 设置主键，后面讨论主键和外键
)
engine=InnoDB  # 指定引擎使用InnoDB，默认不用管
CHARACTER SET utf8mb4  # 指定数据集，默认和数据库用的一样的数据集
COLLATE utf8mb4_general_ci; # 指定校验，默认和数据库用的一样的
```

## 删除表

```mysql
drop table [if exists] table_name_;  # 删除表
truncate table table_name_; # 删除表的所有数据，但是表并没有被删除（结构保留了）
```

## 更改表结构

```mysql
# 新增列，将 database_name.table_name_ 的表新增一列，类型为，且非空
alter table database_name.table_name_ add column `col_name` varchar(10) not null;

# 修改列，将 database_name.table_name_ 表的 source_col_name 改成 `dst_col_name` varchar(20) not null
alter table database_name.table_name_ change column `source_col_name` `dst_col_name` varchar(20) not null;

# 删除列，将 database_name.table_name_ 表的 col_name 删除
alter table database_name.table_name_  drop column `col_name`;
```

# 关系型数据库

## 介绍

MySQL的数据库其实是一种关系型数据库，何谓关系，即`一对一`，`一对多`，`多对一`（注，多对多其实是一对多和多对一的组合）。

关系型数据库最基本的存储容器是表，类似于`excel`软件一样。

想象有三张表，学生表、班级表和班主任表。

学生表对班级表，那就是对多一的关系

班级表对学生表，那就是一对多的关系

班级表与班主任表之间，就是一对一的关系。

上面学的都是MySQL的命令与代码，下面将要学习的是SQL(Structured Query Language)，这是一种编程语言，关系型数据库就是使用这种语言进行交互的，最核心的四个功能就是，增删改查。

下图是SQL语言的命令分类

![image-20250218223459932](https://imagere.oss-cn-beijing.aliyuncs.com/PC_PicGO/20250218223500187.png)

## 关系键

接下来讲讲主键和外键，一张表如何区分两行数据是否相同？只要两行数据有一列位置上的数据不同，那么两行数据就是不同的。但是那就需要整列整列比较了，往往可以通过指定一列作为对比，这样会比较快速。这一列区分不同数据的，称之为`主键`。

一般来说，使用`自增`来作为主键，即`id`。亦或者是使用`UUID`算法来作为主键。千万不要使用业务数据，比如年龄、姓名、身份证号等作为主键。

主键还可以有联合主键，就是使用多列来区分，比如两列，那么主键就是约束这两列不能完全重复。



继续以三张表，学生、班级和班主任表作为例子。

已知，在三张表中，均使用自增`id`作为主键，如何实现学生和班级的对应呢（多对一，多个学生对应一个班级）？那么就可以在学生表中新增一列`班级id`，还可以使用数据库的外键约束来强化这种对应关系，但是外键约束会导致性能下降，所以在应用程序中手动对应这种外键关系会更好。

如何实现一对一的关系呢？（一个班级对应一个班主任），也可以使用外键来对应。



拆表：有时候一张大表，比如学生表，存储了几千个学生，会包括许多数据，比如：姓名、电话、QQ、微信、年龄、班级、家庭等等，也并不是所有学生都填了每一个信息的，比如有些学生没有微信，有些学生不填家庭住址等等。那么就可以单独提取一个微信表，即生成一张新的表`id, 学生id, 微信`。也可以把不常用的数据和常用数据分开，这样加快访问速度。



索引，`mysql`会允许对某一列标记为索引，可以理解成它在插入的时候就排序了，这样查找起来快多了。对于查远远大于增的表，这种方式是很不错的。对于主键是自动建立索引的，因为其是递增的，插入也是非常快的。

# 增

最重要的一条命令`insert`

```mysql
insert into `table_name`  # 插入到表 table_name 中
(`col_name1`, `col_name2`, `col_name3`, ..., `col_namei`)  # 列名（字段名），如果插入的是所有列，那么可以省略
values
(data_1_1, data_1_2, data_1_3, ..., data_1_4),  # 第一条数据，可以插入单条或多条数据
(default, data_2_2, data_2_3, ..., data_2_4),  # 如果是自增数据，那么就可以使用 default 来保证数据自动增1，而不需要人为设置
...
(data_n_1, data_n_2, '2025-02-18', ..., 'this is string');  # 使用单引号或者双引号 '' 来插入字符串，最后一列不要加逗号
```

增删改查中，增应该是最简单的，因为后面的删改都需要进行条件判断，而查则是最难的。

# 删

下面是删除的最基本语法

```mysql
delete from `database.table` # 所有的这种 `database.table` 可以写成 `table` 但是需要进入到数据库中
where <条件>;     # 这里是条件，详细条件用法需要看后面

# 比如下面：
delete from `table` where id = 3;  # 删除 id 为 3 的那一行数据，这里是单个等号
```

# 改

下面是更改的最基本用法

```mysql
update `database.table`  # 使用哪张表
set `col_name` = col_data  # 更新的数据
where <条件>;  # 这里的条件详细用法看后面
```

# 查

查应该是最复杂的用法，但是其实只涉及到一条最基础的命令`select ... from table ...;` 难点在于两个省略号，内容太多了。

接下来还是以[测试用例]( https://gitee.com/eggtoopain/my-sql-introductory-courseware/blob/master/Egg_database.sql)来一步步学习，这个样例一共两张表，创建表代码如下：

```mysql
CREATE TABLE IF NOT EXISTS Covid_total (
    `Country_id` INT NOT NULL AUTO_INCREMENT,
    `Country` VARCHAR(22) CHARACTER SET utf8,
    `Continent` VARCHAR(17) CHARACTER SET utf8,
    `Population` INT,
    `Total_cases` INT,
    `Total_deaths` INT,
    `Total_recovered` INT,
    `Total_active` INT,
    PRIMARY KEY (Country_id)
);

CREATE TABLE IF NOT EXISTS Covid_month (
    `Record_id` INT NOT NULL AUTO_INCREMENT,
    `Date` DATETIME,
    `Country` VARCHAR(32) CHARACTER SET utf8,
    `Confirmed` INT,
    `Deaths` INT,
    `Recovered` INT,
    `Active` INT,
    `New_cases` INT,
    `New_deaths` INT,
    `New_recovered` INT,
    `Continent` VARCHAR(21) CHARACTER SET utf8,
    PRIMARY KEY (Record_id)
);
```



***Ps***:表的来源是，[技术蛋老师](https://www.bilibili.com/video/BV16D4y167TT/?spm_id_from=333.1391.0.0&vd_source=0a579f07fbfd2a261b953858b74e9bfb)

## 简单查询

```mysql
# 查询所有的，这个速度非常慢
select * from Covid_total; 

# 只查询这两列，内容多，速度也慢
select 
	`Country_id`,`Total_cases` 
from Covid_total; 

# 相比于上一句，仅仅是显示重命名了而已，显示 c_id 和 tc
select 
	`Country_id` as `c_id`,  # 显示 c_id 而不是 Country_id
	`Total_cases` as `tc` 
from Covid_total; 

# distinct 是去重，查询结果均相同的去重，比如下面 所有 Continent = Country 的只显示一个
select distinct 
	`Continent`, `Country` 
from Covid_total; 

# 对查询结果排序，ASC是升序，DESC是降序
select 
	`Country_id`, `Country`, `Total_deaths` 
from Covid_total 
order by 
	`Total_deaths` ASC;
# 还可以多列排序，即，从第一个条件开始排序一直到最后一个条件
select 
	`Country_id`, `Country`, `Total_deaths` 
from Covid_total 
order by 
	`Total_deaths` ASC, 
	`Country_id` DESC; # 先按照 Total_deaths 升序，再按照 Country_id 降序
```

## 简单条件查询

支持`=`,`!=`,`<`,`>`,`>=`,`<=`,`and`,`or`,`not`,`between<val1>and<val2>(val1<=val<=val2)`,`IN`,`IS`,`LIKE`等符号

```mysql
# 条件查询应该是如下形式，注：加上了之前的用法，之前的用法不是必须的，只是看看一个 select 是怎么一步步增加的
select [distinct] 
	<col_name> [as `new_name`], ... 
from `table_name`
where       # 这一部分才是条件查询
	<条件>   # 这是条件
[order by
	<排序字段>]
;
```

1.   基本的比较运算符用法以及逻辑运算符用法：

```mysql
# 1.1 普通比较
select 
	`Country_id`, `Country`, `Total_deaths` 
from Covid_total 
where 
	`Country_id` <= 100  # 只看 id 不大于 100 的数据
; 

# 1.2 逻辑运算符
select 
	`Country_id`, `Country`, `Total_deaths` 
from Covid_total 
where 
	`Country_id` <= 100  # 只看 id 不大于 100 的数据
	and `Total_deaths`  between 9252 and 9889  # 而且只查看 Total_deaths 在 [9252, 9889]之间的
; # 如果 between  and 的两个数字大小顺序错了不会报错，只会 没结果

# 1.3 is 比较
select 
	`Country_id`, `Country`, `Total_deaths` 
from Covid_total
where
	`Total_deaths` is null  # 查询为 null 的
; 

# 1.4 in 运算
select 
	`Country_id`, `Country`, `Total_deaths` 
from Covid_total
where
	`Total_deaths` IN (9252, 9889)  # 枚举查询，这样会比较慢，更加推荐使用 = 查询，查询等于 9252 和 9889 的
; 


```

2.   `like`查询，这其实是模糊搜索，即使用通配符`_`和`%`：`_`表示任意单个字符，`%`任意多个字符

```mysql
select 
	`Country_id`, `Country`, `Total_deaths` 
from Covid_total
where
	`Total_deaths` like '9___'  # 注意使用单引号包裹，而且这个不仅可以查询字符串，也可以查询数字，比如查询 9xxx 的数字
; 


select 
	`Country_id`, `Country`, `Total_deaths` 
from Covid_total
where
	[binary] `Country` like 'g%'  # 查询 G 开头的国家，默认不区分大小写的，除非在前面加上 binary，又或者建表的时候在列属性那里加上 binary
; 
```

