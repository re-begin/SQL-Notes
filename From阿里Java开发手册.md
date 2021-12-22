# SQL

>From 阿里Java开发手册
## 建表规约
1. 表达是否概念的字段，使用`is_xxx`的方式命名，数据类型是`unsigned tinyint`（1表示是，0表示否）
    >任何字段为非负数，必须是`unsigned`

    >数据库表示是否的值，使用`tinyint`类型
    例如：`is_deleted`
2. 表名、字段名必须使用小写字母或数字，禁止出现数字开头，禁止两个下划线中间只出现数字
    >MySQL在Windows下不区分大小写，但是Linux下默认区分大小写

    >正例：`aliyun_admin`, `rdc_config`, `level3_name`  
    >反例：`AliyunAdmin`, `rdcConfig`, `level_3_name`
3. 表名不使用复数名词
4. 主键索引名为`pk_字段名`，唯一索引名为`uk_字段名`，普通索引名为`idx_字段名`
    >`pk_` 即primary key，`uk_` 即unique key，`idx_` 即index
5. 小数类型为`decimal`，禁止使用`float`和`double`
    >`float`和`double`都存在精度损失问题
    
    >如果储存的数据范围超过`decimal`的范围，建议将数据拆成整数和小数分开储存
6. 如果储存的字符串长度几乎相等，使用`char`定长字符串类型
7. `varchar`是可变长字符串，不预先分配存储空间，长度不要超过5000，如果存储长度大于此值，定义字段类型为`text`，独立出来一张表，用主键来对应，避免影响其他字段索引效率
8. 合适的字符存储长度，不但节约数据库表空间、节约索引存储，更重要的是提升检索速度
    > 正例：无符号值可以避免误存负数，且扩大了表示范围

    > `tinyint unsigned` 0~255  
    >`smallint unsigned` 0~65535  
    >`int unsigned` 0~约43亿  
    >`bigint unsigned` 0~约10^19

## SQL语句
1. 不要使用`count(列名)`或`count(常量)`来代替`count(*)`，`count(*)`是SQL92定义的标准统计行数的语法，跟数据库无关，跟NULL和非NULL无关

    > `count(*)`会统计值为NULL的行，而`count(列名)`不会统计此列为NULL值的行

2. `count(distinct col)`计算该列除NULL之外的不重复行数，注意`count(distinct col1, col2)`如果其中一列全为NULL，那么即使另一列有不同的值，也返回为0

3. 当某一列的值全是NULL时，`count(col)`的返回结果为0，但`sum(col)`的返回结果为NULL，因此使用`sum()`时需注意NPE问题
    > 可以使用如下方式来避免sum的NPE问题：`select IFNULL(sum(col), 0) from table`

4. 使用`ISNULL()`来判断是否为NULL值
    > NULL与任何值的直接比较都为NULL  
    NULL <> NULL 返回NULL，而不是false  
    NULL = NULL 返回NULL，而不是true  
    NULL <> 1 返回NULL，而不是true

5. `in`操作能避免则避免，若实在避免不了，需要仔细评估in后边的集合元素数量，控制在1000以内
