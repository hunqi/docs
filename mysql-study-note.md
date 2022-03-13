### show create table table_name

mysql> show create table job \G
*************************** 1. row ***************************
       Table: job
Create Table: CREATE TABLE `job` (
  `id` int NOT NULL AUTO_INCREMENT,
  `state` int DEFAULT '0',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=12 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci

## modify table
### modify table name and column
- modify table name
	> alter table old_table_name rename [to] new_table_name
	> alter table t_dept rename tab_dept;
- add column
	> alter table table_name add column_name data_type
	> alter table t_dept add descri varchar(20);
- add column to head
	> alter table table_name add column_name data_type;
	> alter table t_dept add descri varchar(20) first;
- add column after the specifc column
	> alter table table_name add column_name data_type after specific_column_name;
	>  alter table t_dept add descri varchar(20) after deptno;
- drop column
	> alter table table_name drop column_name;
- modify a column to the specific data type
	> alter table table_name modify column_name data_type;
- modify name of a column
	> alter table table_name change old_column_name new_column_name old_data_type;
- modify name and data type of a column
	> alter table table_name change old_column_name new_column_name new_data_type;
- modify order of columns
	> alter table table_name modify column1 data_type first|after column2;

### modify constraint
##### set unique key
- create table tab_name(column_name data_type unique);
- create table tab_name(
	...,
	name varchar(20),
	constraint uk_name unique(name)
);
##### set primary key of a column
- create table tab_name(
	col data_type primary key,
	...
);
- create table tab_name(
	idcard varchar(20),
	...,
	constraint pk_idcard primary key(idcard)
);
##### set primary key of multiple columns
- create table tab_name(
	id int,
	name varchar(20),
	...,
	constraint pk_id_name primary key(id, name)
);

##### set column as auto_increment
- create table tab_name(
	col data_type auto_increment,
	...
);

##### set foreign key
- create table tab1(
	col1 data_type,
	col2 data_type,
	constraint fk_name foreign key(col1) references tab2(tab2_col3)
);

## Index
### situations for creating index
- Often queried colums, which appear after where clause
- Grouping columns, which appear in group by clause
- Related columns, namely primary key column or foreign key column
- Unique column
### situations not suitable for creating index
- Rarely used in where clause
- Column having many repeatable values

#### create index
- create table tab_name(
	...,
	index|key [index_name] (col1 [length] [asc|desc])
);

#### 在创建索引时，可以指定索引的长度。因为不同存储引擎定义了表的最大索引数和最大索引长度

#### MySQL所支持的存储引擎对每个表至少支持16个索引，总索引长度至少为256字节

## dataType
- integers: TINYINT(1 BYTE), SMALLINT(2 BYTES), MEDIUMINT(3 BYTES), INT or INTEGER(4 BYTES), BIGINT(8 BYTES)

### date and time
- type------len_of_bytes------min------max
- DATE, 4, 1000-01-01, 9999-12-31
- DATETIME, 8, 1000-01-01 00:00:00, 9999-12-31 23:59:59
- TIMESTAMP, 4, 19700101080001, 2038年的某个时刻
- TIME, 3, -835:59:59, 838:59:59
- YEAR, 1, 1901, 2155

### characters
- type------len_of_bytes------descr
- CHAR(M), M, M为0~255之间的整数，长度不可变
- VARCHAR(M), M, M为0~65535之间的整数，长度可变

### text types 只能存储字符数据
- type------len_of_bytes------descr
- TINYTEXT, 0~255, 值的长度为+2个字节
- TEXT, 0~65535, 值的长度为+2个字节
- MEDIUMTEXT, 0~167772150, 值的长度为+3个字节
- LONGTEXT, 0~4294967295, 值的长度为+4个字节

### bytearray系列字符串类型: 用于存储少量二进制数据（如图片、音乐、视频文件）
- type------len_of_bytes------descr
- BINARY(M), M, 允许长度为0~M
- VARBINARY(M), M, 允许长度为0~M
- 数据长度经常变化时，可选择VARBINAR类型， 否则BINARY

### BLOB字符串类型: 可以存储二进制数据（如图片、音乐、视频文件）
- type------len_of_bytes
- TINYBLOB, 0~255
- BLOB, 0~2^16
- MEDIUMBLOB, 0~2^24
- LONGBLOB, 0~2^32

### help command
mysql > help contents;
mysql > help data types;
mysql > help int;


### create table
CREATE TABLE t_dept(
deptno INT,
dname VARCHAR(40),
loc VARCHAR(100));

### check table
DESC t_dept;

### insert table
INSERT INTO t_dept VALUES(10, 'ACCOUNTING', 'NEW YORK');

### select table
SELECT ename,sal, job
FROM t_employee 
WHERE (sal, job) = (
	SELECT sal , job 
	FROM t_employee
	WHERE ename = 'SMITH'
);

SELECT *
FROM t_employee 
WHERE deptno NOT IN (
SELECT deptno FROM t_dept
);

SELECT ename, sal
FROM t_employee 
WHERE sal > ANY(SELECT sal FROM t_employee WHERE job='manager');

SELECT ename, sal
FROM t_employee 
WHERE sal > ALL(SELECT sal FROM t_employee WHERE job='manager');

SELECT *
FROM t_dept d 
WHERE NOT EXISTS (SELECT * FROM t_employee e WHERE e.`deptno`=d.`deptno`);

SELECT *
FROM t_dept d 
WHERE EXISTS (SELECT * FROM t_employee e WHERE e.`deptno`=d.`deptno`);

SELECT d.`deptno`, d.`dname`, COUNT(e.`empno`) number, AVG(e.`sal`) average
FROM t_employee e INNER JOIN t_dept d ON e.`deptno`=d.`deptno`
GROUP BY d.`deptno`, d.`dname`, d.`loc`;

SELECT d.`deptno`, d.`dname`, d.loc, employee.number, employee.average
FROM t_dept d INNER JOIN (
	SELECT deptno dno, COUNT(empno) number, AVG(sal) average
	FROM t_employee
	GROUP BY deptno) employee
ON d.`deptno`=employee.dno;

SELECT COUNT(*) number
FROM t_dept d, (
	SELECT deptno dno, COUNT(empno) number, AVG(sal) average
	FROM t_employee
	GROUP BY deptno) employee;

SELECT COUNT(*)
FROM t_dept d JOIN t_employee e ON d.`deptno`=e.`deptno`;

### grant privilege in MySQL8
Grant SELECT, insert, create,delete,update,alter,drop on job.* to 'rs'@'localhost' with grant option;

### create table
create table t_candidate(
	id int not null auto_increment,
	name varchar(40) not null,
	age int(3),
	constraint pk_cid primary key (id)
) engine=InnoDB DEFAULT charset=gbk;

### 备份某个数据库
mysqldump -u root -p --databases job>c:\database_job_back.sql

### 备份所有数据库
mysqldump -u root -p --all-databases>c:\all_databases_all.sql

### 根据文件还原数据
mysql -uroot -p <C:\database_job_back.sql

### 20220125
mysql> select * from t_dept
    -> into outfile 'c:/t_dept.txt';
ERROR 1290 (HY000): The MySQL server is running with the --secure-file-priv option so it cannot execute this statement

### show variables
show variables like '%secure%';