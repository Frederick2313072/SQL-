---
title: SQL必知必会例子
description:
date: 2024-12-05


---



```sql

SELECT RTRIM(vend_name) + '('+   --右边取消空格
RTRIM(vend_country) + ')'
  AS vend_title
From Vendors
ORDER BY vend_name;
SELECT prod_id,quantity,item_price,quantity*item_price
AS expanded_price
FROM OrderItems;
SELECT vend_name,UPPER(vend_name) --大写
AS vend_name_upcase
FROM Vendors
ORDER BY vend_name;
SELECT cust_name,cust_contact
FROM Customers
WHERE SOUNDEX(cust_contact) = SOUNDEX('Michael Green'); --近似发音
SELECT order_num
FROM Orders
WHERE DATEPART(yy,order_date) = 2020;  
SELECT AVG(prod_price) AS avg_price --取平均值
FROM Products
WHERE vend_id = 'DLL01';
SELECT COUNT(*)AS num_cust --对所有行计数
FROM Customers;
SELECT MAX(prod_price) AS max_price
FROM Products;
SELECT SUM(quantity*item_price) AS
items_ordered
FROM OrderItems
WHERE order_num = 20005;
SELECT AVG(DISTINCT prod_price) AS avg_price
FROM Products
Where vend_id ='DLL01';

SELECT COUNT(*) AS num_items,MIN(prod_price) AS price_min,MAX(prod_price) AS proce_max,AVG(prod_price) AS price_avg --组合聚集函数
FROM products;
SELECT vend_id,COUNT(*) AS num_prods
FROM Products
Group BY vend_id
HAVING COUNT(*) >= 2;--过滤分组，group by 和 having结合

SELECT order_num,COUNT(*)AS items
FROM OrderItems
Group BY order_num
HAVING COUNT(*) >= 3--检索包含三个或更多物品的订单号和订购物品的数目
ORDER BY items,order_num;--按订购物品的数目排序输出，having在group by前，order by后

SELECT cust_id
FROM OrderItems
WHERE prod_id = 'RGAN01';
SELECT cust_id
FROM Orders
WHERE order_num IN (20007,20008);
SELECT cust_id
FROM Orders
WHERE order_num IN (SELECT cust_id
FROM Orders
WHERE order_num IN (20007,20008));--建立以上的子查询，由内向外

SELECT cust_name,cust_state,(SELECT COUNT(*)FROM Orders WHERE Orders.cust_id=Customers.cust_id) AS orders --子查询对检索出的每个顾客执行一次，用句点避免混淆列名
FROM Customers
ORDER BY cust_name;--对于检索出的每个顾客，统计其在Orders表中的订单数目

SELECT vend_name,prod_name,prod_price
FROM Vendors,Products
WHERE Vendors.vend_id = Products.vend_id;

BEGIN TRANSACTION
DELETE OrderItems WHERE order_num =12345
SAVE TRANSACTION delete1;--使用保留点delete1，保留点越多越好，额能进行灵活的回退
DELETE Orders
WHERE order_num =12345
ROLLBACK TRANSACTION delete1;--回退到保留点delete1
COMMIT TRANSACTION--控制事物处理，最后的commit仅在中间过程不出错的情况下写出更改
BEGIN TRANSACTION
DELETE OrderItems WHERE order_num =12345
SAVE TRANSACTION delete1;--使用保留点delete1，保留点越多越好，额能进行灵活的回退
DELETE Orders
WHERE order_num =12345
ROLLBACK TRANSACTION delete1;--回退到保留点delete1
COMMIT TRANSACTION--控制事物处理，最后的commit仅在中间过程不出错的情况下写出更改
DECLARE CURSOR CustCursor
IS
SELECT * FROM Customers
WHERE cust_email IS NULL;
ALTER TABLE Vendors
ADD CONSTRAINT 
PRIMARY KEY(vend_id)--设置主键，DBMS默认主键基本不修改或更新，不能重用
ALTER TABLE Orders
ADD CONSTRAINT 
FOREIGN KEY (cust_id) REFERENCES Customers(cust_id);--将此表中的cust_id设置为Customers的主键，即外键。外键有助防止意外删除，若删除，级联删除
CREATE INDEX prod_name_ind--索引名
ON Products(prod_name)--被索引的表，列
CREATE TRIGGER customer_state
ON Customers
FOR INSERT,UPDATE
AS
UPDATE Customers
SET cust_state=Upper(cust_state)
WHERE Customers.cust_id = inserted.cust_id;--创建一个触发器，对所有INSERT和UPDATE操作，将cust_state转为大写
```

