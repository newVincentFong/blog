# SQL增删改查、联表查询
## 前言 

作为前端人员，时不时要包办一下后端，写写增删改查的api等等

由于SQL操作早已被各个语言的库所封装，原生SQL语法容易年久生疏

因此写了一套 `SQL军体拳` ，循序渐进跟着打一套，便能重拾雄风，应付面试、考核之需

（难度仅去到联表，事务、锁什么的未包含）

## `SELECT`

先从最简单的开始噢

* 拖个表出来
```SQL
SELECT * FROM `account`
```



* 拖需要的列 （列名上引号可有可无）
```SQL
SELECT account_id, password FROM `account`
```



* 拖单列，但去重
```SQL
SELECT DISTINCT nickname FROM `account`
```



* 条件查询 （字符串要带单引号）
```SQL
SELECT * FROM `account` WHERE mobile='18077779999'
```
> 条件可以拼接`AND`、`OR`，条件可以是 `>=`、`IN (0,1)`、 `LIKE '%模糊搜索%'`、 `BETWEEN...AND...`

> 条件多了可以加个括号，防止歧义



* 排个序 （默认是顺序，加 `DESC` 是倒序）
```SQL
SELECT * FROM `account` ORDER BY `account_id` DESC
```



* 分个页 （第一页、第二页、第三页...）
```SQL
SELECT * FROM `account` LIMIT 0, 10
SELECT * FROM `account` LIMIT 9, 10
SELECT * FROM `account` LIMIT 19, 10
```



* 分个组 （配合求和`sum`、求平均`avg`等等）
```SQL
SELECT sum(`pay_points`) AS total, `account_id` FROM `md_account` GROUP BY `account_id`
```



* 联表、条件复杂的长SQL语句，用到`JOIN`、`ALIAS`指令 （其实不提倡`JOIN`大表）
```SQL
SELECT * 
FROM `md_account` AS `a`
JOIN `md_orders` AS `o`
ON a.account_id = o.account_id 
```
> `JOIN`的形式有很多种

> `INNER JOIN` 返回的集合最小

> `LEFT JOIN` 条件不成立，也要返回命令左侧的表保底

> `RIGHT JOIN` 条件不成立，也要返回命令右侧的表保底

> `FULL JOIN` 返回的集合最大

## `UPDATE`
* 改某表某行某值
```SQL
UPDATE `md_account` SET nickname = 'god' WHERE `account_id` = 332;
```


## `INSERT`
* 新建某行数据
```SQL
INSERT INTO `md_account` (username, password) VALUES('god','123abc');
```

## `DELETE`
* 删除某行数据
```SQL
DELETE FROM `md_account` where account_id = 1;
```