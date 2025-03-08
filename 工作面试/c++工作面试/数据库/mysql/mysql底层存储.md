# 数据库存储

```mysql
SHOW VARIABLES LIKE 'datadir'; // 查看存储位置，一般 /var/lib/mysql/
```

没创建一个数据库，会在`/var/lib/mysql`下面创建以数据库名的文件夹，里面一般有`dp.opt`, `table_name.frm`, `table_name.ibd`。

-   `dp.opt`：数据库字符和校验规则
-   `.frm`：表结构的元数据，每建立一张表，都会有一个文件
-   `.ibd`：表数据实际存储位置，称之为表空间，表空间由段、区、页、行的层级结构组成

`InnoDB`的逻辑存储结构如下：

![img](https://imagere.oss-cn-beijing.aliyuncs.com/PC_PicGO/20250307233503273.png)

# 行存储

## 行格式

`InnoDB`有四种行格式：`Redundant`, `Compact`, `Dynamic`和`Compressed`。

`Redundant`：有点老了，已经不再用了，而且不紧凑

`Compact`：紧凑的行格式

`Dynamic, Compressed`：都是基于`compact`的改进，默认是`Dynamimc`



从`Compact`入手就很容易懂了，如下图所示

![img](https://imagere.oss-cn-beijing.aliyuncs.com/PC_PicGO/20250307234807013.png)

-   记录头信息：保存着删除标志、下一条记录的位置（记录头和记录真实数据之间的位置）、记录类型等等
-   `NULL`值列表：**从右往左**依次代表**从左往右**的列是否为`NULL`，用比特位表示，不足就填充到一个字节。
-   变长字段长度列表：**从右往左**依次代表**从左往右**的可变长列的长度，如果变长字段最大长度小于等于255，就用一个字节表示，否则就用两个字节表示。
-   `row_id,trx_id,roll_ptr`：都是在事务需要用到的
-   `列值`：如果太长，则需要使用溢出页来存储额外部分，会使用20个字节来存储溢出页位置。

***PS***：`右->左`和`左->右`是为了提高缓存命中率

# 页存储

![图片](https://imagere.oss-cn-beijing.aliyuncs.com/PC_PicGO/20250307235604195.png)

# B+树存储

B+树以页为一个节点的，存储如下：

![图片](https://imagere.oss-cn-beijing.aliyuncs.com/PC_PicGO/20250307235640310.png)