---
layout: post
title: "mysql in 子查询 效率慢 优化"
date: 2015-03-05 13:00
comments: false
---

现在的CMS系统、博客系统、BBS等都喜欢使用标签tag作交叉链接，因此我也尝鲜用了下。但用了后发现我想查询某个tag的文章列表时速度很慢，达到5秒之久！百思不解(后来终于解决)，我的表结构是下面这样的，文章只有690篇。

文章表article(id,title,content)
标签表tag(tid,tag_name)
标签文章中间表article_tag(id,tag_id,article_id)
其中有个标签的tid是135，我帮查询标签tid是135的文章列表
用以下语句时发现速度好慢,我文章才690篇
	select id,title from article where id in(
	select article_id from article_tag where tag_id=135
	)
其中这条速度很快：select article_id from article_tag where tag_id=135
查询结果是五篇文章，id为428,429,430,431,432
我用写死的方式用下面sql来查文章也很快
	select id,title from article where id in(
	428,429,430,431,432
	)
我在SqlServer中好像不会这样慢，不知MySQL怎样写好点，也想不出慢在哪里。

后来我找到了解决方法：

	select id,title from article where id in(
	select article_id from (select article_id from article_tag where tag_id=135) as tbt
	)

 

 

其它解决方法：（举例）

	mysql> select * from abc_number_prop where number_id in (select number_id from abc_number_phone where phone = '82306839');

为了节省篇幅，省略了输出内容，下同。

67 rows in set (12.00 sec)


只有67行数据返回，却花了12秒，而系统中可能同时会有很多这样的查询，系统肯定扛不住。用desc看一下(注：explain也可)


	mysql> desc select * from abc_number_prop where number_id in (select number_id from abc_number_phone where phone = '82306839');
	+----+--------------------+------------------+--------+-----------------+-------+---------+------------+---------+--------------------------+
	| id | select_type | table | type | possible_keys | key | key_len | ref | rows | Extra |
	+----+--------------------+------------------+--------+-----------------+-------+---------+------------+---------+--------------------------+
	| 1 | PRIMARY | abc_number_prop | ALL | NULL | NULL | NULL | NULL | 2679838 | Using where |
	| 2 | DEPENDENT SUBQUERY | abc_number_phone | eq_ref | phone,number_id | phone | 70 | const,func | 1 | Using where; Using index |
	+----+--------------------+------------------+--------+-----------------+-------+---------+------------+---------+--------------------------+
	2 rows in set (0.00 sec)


从上面的信息可以看出，在执行此查询时会扫描两百多万行，难道是没有创建索引吗，看一下


	mysql>show index from abc_number_phone;
	+------------------+------------+-------------+--------------+-----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
	| Table | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
	+------------------+------------+-------------+--------------+-----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
	| abc_number_phone | 0 | PRIMARY | 1 | number_phone_id | A | 36879 | NULL | NULL | | BTREE | | |
	| abc_number_phone | 0 | phone | 1 | phone | A | 36879 | NULL | NULL | | BTREE | | |
	| abc_number_phone | 0 | phone | 2 | number_id | A | 36879 | NULL | NULL | | BTREE | | |
	| abc_number_phone | 1 | number_id | 1 | number_id | A | 36879 | NULL | NULL | | BTREE | | |
	| abc_number_phone | 1 | created_by | 1 | created_by | A | 36879 | NULL | NULL | | BTREE | | |
	| abc_number_phone | 1 | modified_by | 1 | modified_by | A | 36879 | NULL | NULL | YES | BTREE | | |
	+------------------+------------+-------------+--------------+-----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
	6 rows in set (0.06 sec)

	mysql>show index from abc_number_prop;
	+-----------------+------------+-------------+--------------+----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
	| Table | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
	+-----------------+------------+-------------+--------------+----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
	| abc_number_prop | 0 | PRIMARY | 1 | number_prop_id | A | 311268 | NULL | NULL | | BTREE | | |
	| abc_number_prop | 1 | number_id | 1 | number_id | A | 311268 | NULL | NULL | | BTREE | | |
	| abc_number_prop | 1 | created_by | 1 | created_by | A | 311268 | NULL | NULL | | BTREE | | |
	| abc_number_prop | 1 | modified_by | 1 | modified_by | A | 311268 | NULL | NULL | YES | BTREE | | |
	+-----------------+------------+-------------+--------------+----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
	4 rows in set (0.15 sec)


从上面的输出可以看出，这两张表在number_id字段上创建了索引的。


看看子查询本身有没有问题。

	mysql> desc select number_id from abc_number_phone where phone = '82306839';
	+----+-------------+------------------+------+---------------+-------+---------+-------+------+--------------------------+
	| id | select_type | table | type | possible_keys | key | key_len | ref | rows | Extra |
	+----+-------------+------------------+------+---------------+-------+---------+-------+------+--------------------------+
	| 1 | SIMPLE | abc_number_phone | ref | phone | phone | 66 | const | 6 | Using where; Using index |
	+----+-------------+------------------+------+---------------+-------+---------+-------+------+--------------------------+
	1 row in set (0.00 sec)


没有问题，只需要扫描几行数据，索引起作用了。查询出来看看

	mysql> select number_id from abc_number_phone where phone = '82306839';
	+-----------+
	| number_id |
	+-----------+
	| 8585 |
	| 10720 |
	| 148644 |
	| 151307 |
	| 170691 |
	| 221897 |
	+-----------+
	6 rows in set (0.00 sec)


直接把子查询得到的数据放到上面的查询中

	mysql> select * from abc_number_prop where number_id in (8585, 10720, 148644, 151307, 170691, 221897);

	67 rows in set (0.03 sec)


速度也快，看来MySQL在处理子查询的时候是不够好。我在MySQL 5.1.42 和 MySQL 5.5.19 都进行了尝试，都有这个问题。


搜索了一下网络，发现很多人都遇到过这个问题：

参考资料1：使用连接（JOIN）来代替子查询(Sub-Queries) mysql优化系列记录
http://blog.csdn.net/hongsejiaozhu/article/details/1876181
参考资料2：网站开发日记(14)-MYSQL子查询和嵌套查询优化
http://dodomail.iteye.com/blog/250199

根据网上这些资料的建议，改用join来试试。

修改前：select * from abc_number_prop where number_id in (select number_id from abc_number_phone where phone = '82306839');

修改后：select a.* from abc_number_prop a inner join abc_number_phone b on a.number_id = b.number_id where phone = '82306839';


	mysql> select a.* from abc_number_prop a inner join abc_number_phone b on a.number_id = b.number_id where phone = '82306839';

	67 rows in set (0.00 sec)


效果不错，查询所用时间几乎为0。看一下MySQL是怎么执行这个查询的


	mysql>desc select a.* from abc_number_prop a inner join abc_number_phone b on a.number_id = b.number_id where phone = '82306839';
	+----+-------------+-------+------+-----------------+-----------+---------+-----------------+------+--------------------------+
	| id | select_type | table | type | possible_keys | key | key_len | ref | rows | Extra |
	+----+-------------+-------+------+-----------------+-----------+---------+-----------------+------+--------------------------+
	| 1 | SIMPLE | b | ref | phone,number_id | phone | 66 | const | 6 | Using where; Using index |
	| 1 | SIMPLE | a | ref | number_id | number_id | 4 | eap.b.number_id | 3 | |
	+----+-------------+-------+------+-----------------+-----------+---------+-----------------+------+--------------------------+
	2 rows in set (0.00 sec)


小结：当子查询速度慢时，可用JOIN来改写一下该查询来进行优化。


网上也有文章说，使用JOIN语句的查询不一定总比使用子查询的语句快。

参考资料3：改变了对Mysql子查询的看法
[http://hi.baidu.com/yzx110/blog/item/e694f536f92075360b55a92b.html](http://hi.baidu.com/yzx110/blog/item/e694f536f92075360b55a92b.html)

 

 

mysql手册也提到过，具体的原文在mysql文档的这个章节：

I.3. Restrictions on Subqueries

13.2.8. Subquery Syntax

摘抄：

1）关于使用IN的子查询：

Subquery optimization for IN is not as effective as for the = operator or for IN(value_list) constructs.

A typical case for poor IN subquery performance is when the subquery returns a small number of rows but the outer query returns a large number of rows to be compared to the subquery result.

The problem is that, for a statement that uses an IN subquery, the optimizer rewrites it as a correlated subquery. Consider the following statement that uses an uncorrelated subquery:

SELECT ... FROM t1 WHERE t1.a IN (SELECT b FROM t2);

The optimizer rewrites the statement to a correlated subquery:

SELECT ... FROM t1 WHERE EXISTS (SELECT 1 FROM t2 WHERE t2.b = t1.a);

If the inner and outer queries return M and N rows, respectively, the execution time becomes on the order of O(M×N), rather than O(M+N) as it would be for an uncorrelated subquery.

An implication is that an IN subquery can be much slower than a query written using an IN(value_list) construct that lists the same values that the subquery would return.

2）关于把子查询转换成join的：

The optimizer is more mature for joins than for subqueries, so in many cases a statement that uses a subquery can be executed more efficiently if you rewrite it as a join.

An exception occurs for the case where an IN subquery can be rewritten as a SELECT DISTINCT join. Example:

SELECT col FROM t1 WHERE id_col IN (SELECT id_col2 FROM t2 WHERE condition);

That statement can be rewritten as follows:

SELECT DISTINCT col FROM t1, t2 WHERE t1.id_col = t2.id_col AND condition;

But in this case, the join requires an extra DISTINCT operation and is not more efficient than the subquery