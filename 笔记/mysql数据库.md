
```sql
--获取某个表的创建时间和最后更新时间
SELECT TABLE_NAME, CREATE_TIME, UPDATE_TIME
FROM information_schema.TABLES
WHERE TABLE_SCHEMA = 'your_database' AND TABLE_NAME = 'your_table';

--删除数据
DELETE FROM amazon_merchant_detail WHERE seller_name = "" and business_name = "" and business_address = "";

--更新数据
UPDATE 表名
SET 列名1 = 值1, 列名2 = 值2, ...
WHERE 条件;

--删除重复的某几列数据
DELETE t1
FROM my_table t1
JOIN my_table t2 
ON t1.column1 = t2.column1 
AND t1.column2 = t2.column2 
AND t1.id > t2.id;


```

