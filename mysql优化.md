# 慢查询

## 查看慢查询

```mysql
show VARIABLES like 'slow_query_log';
```

## 配置文件

打开E:\mysql\配置文件.ini文件

添加或者修改配置文件

```mysql
#--是否开启慢查询日志,0->off,1->on
slow_query_1og=on

# --指定保存路径及文件名，默认为数据文件目录
slow_ query_log_file="bxg_mysql_slow.log"

#--指定多少秒返回查询的结果为慢查询
long_query_time=1

#--记录所有没有使用到索引的查询语句
log_queries_not_using_ indexes=1

#--记录那些由于查找了多于1000次而引发的慢查询
min_examined_row_limit=1000

# --记录那些慢的optimize table， analyze table和alter table语句
log_slow_admin_statements=1

#--记录由Slave所产生的慢查询
log_slow_slave_statements=1
```

然后重启mysql服务即可

## 命令行

```mysql
set global slow_query_log=1;
set global slow_query_log_file='bxg_mysql_slow.log';
set long_query_time=1;
set global log_queries_ not_using_indexes=1;
set global min_examined_row_limit=1000;
set global log_slow_admin_statements=1;
set global log_slow_slave_statements= 1; 
```

## 配置文件修改和命令行修改的区别

​				1.配置文件,配置之后需要重启MySQL服务,永久生效

​				2.命令行修改配置方式不需要重启即可生效,但重启之后会自动失效

# 清除缓存

```mysql
# 设置缓存大小
set global_query_cache_size=0;		
# 设置缓存功能,0->off,1->on
set global_query_cache_type=0;
```

整个数据库重启之后第一 执行sql语句, 考虑到第一次磁盘读写和数据库启动时间.



为了提高速度,可以暂停事务.测试添加100万随机数据,大概600s左右时间.

```mysql
-- 停掉事务
set autocommit = 0;
call generate(1000000)
-- CALL genDepAdd(1000000);
-- 重启事务
set autocommit = 1;
```



| 字段           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| Id             | 查询中执行select子句或操作表的顺序<br />数字越高执行快       |
| Select_ type   | 所使用的SELECT查询类型，包括以下常见类型:SIMPLE、PRIMARY、 SUBQUERY、 UNION、<br />DERIVED、 UNION RESULT、DEPENDENT、UNCACHEABLE |
| table          | 所使用的的数据表的名字                                       |
| type           | 表示MySQL在表中找到所需行的方式，又称“访问类型”。取值按优劣排序<br/>为NULL>system > const > eg_ ref ()> ref (找到首字拼音,比range更好)> fulltext >ref_ or_null ><br/>index_merge > unique_ subquery > index_ subquery > range (根据首字的首字母查询)<br />> index(扫描所有的索引,找拼音或者难查字) > ALL(效率特别低,需要对表中所有的数据全部查询才能找到) |
| Possible_ keys | ==可能==使用哪个索引在表中找到记录                           |
| key            | 实际使用的索引(如果为空就要考虑优化,没有用到索引)            |
| Key_ len       | 索引中使用的字节数                                           |
| ref            | 显示索引的哪一列被使用了                                     |
| rows           | 估算的找到所需的记录所需要读取的行数                         |
| filtered       | 通过条件过滤出的行数的百分比估计值                           |
| extra          | 包含不适合在其他列中显示但十分重要的额外信息                 |

![image-20210507223531136](mysql%E4%BC%98%E5%8C%96.assets/image-20210507223531136.png)

