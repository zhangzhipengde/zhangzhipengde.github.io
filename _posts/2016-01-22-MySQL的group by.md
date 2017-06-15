---
layout: post
title: MySQL的group by
date: 2016-01-22 
tag: MySQL
---

**首先有employee这么一张表 **

id | name | dept  | salary | edlevel | hiredate
----|----|----|----
1  | 张三  | 开发部 | 2000  | 3  | 2009-10-11
2  | 李四  | 开发部 | 2500  | 3  | 2009-10-01
3  | 王五  | 设计部 | 2600  | 5  | 2010-10-02
4  | 王六  | 设计部  | 2300  | 4  | 2010-10-03
5  | 马七  | 设计部 | 2100  | 4  | 2010-10-06
6 |  赵八  | 销售部 | 3000  | 5  | 2010-10-05
7  | 钱九  | 销售部 | 3100  | 7  | 2010-10-07
8  | 孙十  | 销售部 | 3500  | 7  | 2010-10-06

**根据指定列的值进行分组**

计算有哪些部门
> SELECT dept FROM employee GROUP BY dept

得到的值为
dept
开发部
设计部
销售部

**SELECT子句中的列名必须为分组列或列函数。列函数对于GROUP BY子句定义的每个组各返回一个结果**

计算 每个部门最高薪水的结果
> SELECT dept,MAX(salary) FROM employee GROUP BY dept

得到的值为


dept | max(salary)
----|----|----
开发部 | 2500
设计部 | 2600
销售部 | 3500

**将 WHERE 子句与 GROUP BY 子句一起使用**

分组查询可以在形成组和计算列函数之前具有消除非限定行的标准 WHERE 子句。必须在GROUP BY 子句之前指定 WHERE 子句。
例如 : 计算 查询公司2010年入职的各个部门每个级别里的最高薪水

>SELECT DEPT, EDLEVEL, MAX( SALARY ) AS MAXIMUM
FROM employee
WHERE HIREDATE > '2010-01-01'
GROUP BY DEPT, EDLEVEL


得到的值为


DEPT | EDLEVEL | MAXIMUM
----|----|---
设计部 | 4 | 2300
设计部 | 5 | 2600
销售部 | 5 | 3000
销售部 | 7 |  3500

<font color="red">注意：在SELECT语句中指定的每个列名也在GROUP BY子句中提到。未在这两个地方提到的列名将产生错误。
GROUP BY子句对DEPT和EDLEVEL的每个唯一组合各返回一行。</font>

**在GROUP BY子句之后使用HAVING子句**

1. 寻找部门员工数超过2个员工的部门,并且求出最高和最低薪水

> SELECT DEPT, MAX( SALARY ) AS MAXIMUM, MIN( SALARY ) AS MINIMUM
FROM employee
GROUP BY DEPT
HAVING COUNT( * ) >2
ORDER BY DEPT

得到的值为



DEPT | MAXIMUM | MINIMUM
--|----|----|----
设计部 | 260 | 2100
销售部 | 3500 | 3000

解析 : 
`先根据部门分组得到最高的最低工资,然后再用having再内存中找出员工数超过2个员工的部门`

2. 寻找雇员平均工资大于3000的部门的最高和最低薪水

>SELECT DEPT,MAX( SALARY ) AS MAXIMUM, MIN( SALARY ) AS MINIMUM
FROM employee
GROUP BY DEPT
HAVING AVG( SALARY ) >3000
ORDER BY DEPT



得到的值为


DEPT | MAXIMUM | MINIMUM
----|----|----
销售部 | 3500 | 3000

解析 : 
     `先根据部门分组得到最高的最低工资,然后再用having再内存中找出平均工资大于3000的员工`
