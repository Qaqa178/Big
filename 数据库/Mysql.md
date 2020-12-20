以管路员方式进入命令行

MySQL停止：net stop  名称

MySQL开启：net start 名称





链接数据库方式：

​		一：命令行： mysql 【-h主机名 -P端口号 】-u用户名 -p密码



- 常见命令

  - 查看当前所有的数据库

    - show databases;

  - 打开指定的库：use 库名；

  - 查看当前库的所有表：show tables;

  - 查看其它库的所有表：select tables from 库名；

  - 创建表：create table 表明(

    列名  列类型，

    ……

    )；

  - 查看表结构：desc 表名；

- MYSQL的语法规范

  - 不区分大小写，但建议关键字大写，表名、列名小写
  - 每条命令最好用分号结尾
  - 每条命令根据需要，可以进行缩进 或换行
  - 注释
    - 单行注释：#注释文字      或   --   注释文字
    - 多行注释：/*注释文字*/

-h(host   主机)

-P(port:  端口号)

```mysql
#进阶1：基础查询
/*
select 查询列表 from 表名;

类似于：System.out.println(打印的数据);

特点：
1.查询列表可以是：表中的字段、常量值、表达式、函数
2.查询的结果是一个虚拟的表格
*/

#打开库名
USE myemployees;

#1.查询表中的单个字段
SELECT last_name FROM employees;

#2.查询表中的多个字段
SELECT last_name,salary,email FROM employees;
```





```mysql
#3.查询表中的所有字段
SELECT
  `first_name`,
  `last_name`,
  `email`,
  `job_id`,
  `phone_number`,
  `salary`,
  `commission_pct`,
  `manager_id`,
  `department_id`,
  `hiredate`
FROM
  employees;

SELECT * FROM employees;
```





```mysql
#4.查询常量值
SELECT 100;

#5.查询表达式
SELECT 100 * 97;

#6.查询函数
SELECT VERSION();

#7.起别名
/*
①便于理解
②如果要查询的字段有重名的情况，使用别名可以区分开来
*/
#方式一：使用AS
SELECT 100%98 AS 结果；
SELECT last_name AS 姓,first_name AS 名 FROM employees;

#方式二：使用空格
SELECT last_name 姓,first_name 名 FROM employees;

#案例:查询salary，显示结果为 out put
SELECT salary AS "out put" FROM employees;



#8.去重:select distinct 查询数据 from 表

#案例：查询员工表中涉及到所有的部门的编号
SELECT DISTINCT department_id FROM employees;

- 的作用

/*

java中的+：
	①运算符：两个操作数为数值型
	②拼接符：只要其中一个操作数为字符串
	
mysql中的+：仅仅只有一个功能：运算符
select 100+100;两个操作数为数值型，则做加法运算
select '123' + 90:有其中一方为字符型，它会试图将字符型数值转换成数值型
		如果转换成功，则继续做加法运算
		如果转换失败，则字符型数值转换成0；
		
select 'Join' + 90;

select null + ?;  只要其中一方为null，则结果肯定为null；

*/
#案例:产寻元名和姓连成一个字段，并显示为姓名

SELECT CONCAT('a','b','c') AS 结果;
SELECT
  CONCAT(last_name , first_name) AS 姓名
FROM
  employees;
```



```mysql
#进阶2：条件查询
/*

语法：
	select 
		查询列表 
	from 
		表明 
	where
		筛选条件；

分类：
	一：按条件表达式筛选
		条件运算符：< > = !=(<>) >= <=
	二：按逻辑表达式筛选
	逻辑运算符：用于连接条件表达式
		逻辑运算符：
		&&(and) 
		||(or) 
		!!(not)
	
	三:模糊查询
		like
		between and
		in 
		is null

*/


#1.按条件表达式筛选

#案例1：查询工资>12000的员工信息
SELECT * FROM employees WHERE salary > 12000;

#案例2：查询部门编号不能与90号的员工和部门编号
SELECT 
	last_name,
	department_id
FROM 
	employees
WHERE
	department_id <> 90;


#按逻辑表达式筛选

#案例：查询工资在10000 - 20000之间的员工名，工资以及奖金

SELECT 
	last_name,
	salary,
	`commission_pct`

FROM 
	employees

WHERE 
	salary >= 10000 && salary <= 20000;

#案例2：部门编号不是在90 - 110之间的或者工资高于15000的员工信息
select 
	*
from
	employees
where
	department_id <= 90 || department_id >= 110
	||salary >= 15000;


#三：模糊查询
/*

like
特点：
	①和通配符搭配使用
		通配符： 
		% 包含任意字符,包含0个字符
		_包含任意一个字符
between and
in
in null | is not null


*/
#案例1：查询员工名中包含字符a的员工信息

select 
	*
from 
	employees
where
	last_name like '%a%'; #abc



#案例2：查询员工名中第三个字符为n,第五个字符为l的员工名和工资
select
	last_name,
	salary
from 
	employees
where 
	last_name like '__n_l%';


#案例三：查询员工名中第二个字符为_的员工名
#ESCAPE转义
select
	last_name
from
	employees
where
	last_name like '_$_%' escape '$';


#between and：在（）之间
/*
①使用between and 可以提高代码的简洁度
②包含临界值
③不要调换临界值


*/

#案例1：查询员工编号在100 - 120之间的员工信息

select
	*
from 
	employees
	
where
	employee_id between 100 and 120;


#3.in
/*
含义：判断某字段的值是否属于in列表中的某一项
特点：
	①使用in提高语句的简洁度
	②in列表的值类型必须是一致或兼容
*/
#案例：查询员工的工种是 IT_PROG、AD_VP、AD_PRES中的一个的员工名和工种编号
select
	last_name,
	job_id
from 
	employees
	
where
	job_id in ('IT_PROG','AD_VP','AD_PRES');



#4.is null
#案例1：查询没有奖金的员工名和奖金
#错误
select
	last_name,
	commission_pct
from
	employees
where
	commission_pct is null;


#案例2：查询有奖金的员工名和奖金

SELECT
	last_name,
	commission_pct
FROM
	employees
WHERE
	commission_pct IS not NULL;


#安全等于:<=>
#查询工资为12000的员工信息
select
	*
from 
	employees
where
	salary <=> 12000;





```

