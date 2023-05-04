2023/4/24

1.opengauss 客户端连接方式。
2.PKI/CA 体系架构及其工作机制。
3.opengauss 安全认证配置。



数据库安全-客户端双向认证
1.证书
2.密钥

对称密钥和非对称密钥
对称密钥：私钥表示个人私有的密钥，信息的发送方和接收方用同一个密钥加密和解密，最大优势是加/解密钥速度快，适合对大数据量进行加密，密钥管理困难

非对称密钥：公钥加密（公开发布），私钥解密（用户自己保存），用一对密钥分别完成加密和解密操作，信息发送者用公开密钥去加密，信息接收者用私用密钥去解密，加密和解密比对称密钥加密慢得多。

CA认证中心：个体不能够互信的情况，公证


数字信封：指发送方使用接收方的公钥来加密对称密钥后所得的数据，其目的是用来确保对称密钥传输的安全性。采用数字信封时，接收方需要使用自己的私钥才能打开数字信封得到对称密钥。

数据加密，对称密钥给对方，用接收方公钥加密对称密钥放进数字信封，数字信封传输后用接收方私钥解密数字信封获得对称密钥。
数字签名：发送方用自己的私钥对数字指纹进行加密后所得的数据。确定发送方。
数字指纹：发送方使用hash算法对明文信息计算后得出的数据，采用数据指纹时，发送方会将数据指纹和明文一起发送给接收方，接受方用同样的hash算法对明文生成的数据进行核验。保证数据的完整性。

数字证书：确定公钥的拥有者是谁。
PKI

认证机构的CA类型

自签名CA：证书中的公钥和用于验证证书公钥是相同的。

从属CA：证书中的公钥和用于验证证书签名的公钥是不同的。

根CA：证书层次结构的最高层，根CA必须对自己的证书签名。

SSL:安全套阶层，TLS是SSL的继承

SSL/TLS传输层协议



https://blog.csdn.net/qq_36963950/article/details/106040590/

https://www.bilibili.com/read/cv17860517

2023/4/25

复盘CA/PKI

根据国赛出题方向进行练习、讲解：

1.多表关联查询：

 union all不会去除重复记录

left join左外连接：以左表为基础，根据ON后给出的两表的条件将两表连接起来

right join右外连接：以右表为基础，根据ON后给出的两表的条件将两表连接起来

内连接：组合两个表中的记录，返回关联字段相符的记录，返回两个表的交集部分

自连接，子查询。



2023/4/26

存储过程：SQL和PL/SQL的组合。代码存储一次能够被多个程序使用。

loop

while_loop::=只要条件表达式为真，WHILE语句就会不停的在一系列语句上进行循环，在每次进入循环体的时候进行条件判断。

//创建表格

CREATE TABLE graderecord   (    number INTEGER,    name CHAR(20),    class CHAR(20),    grade INTEGER );

//定义存储过程

CREATE PROCEDURE insert_data  (param1 INT = 0, param2 CHAR(20),param3 CHAR(20),param4 INT = 0 )  IS BEGIN  INSERT INTO graderecord VALUES(param1,param2,param3,param4);   END; /

//调用存储过程

//CALL  insert_data(param1:=210101,param2:='Alan',param3:='21.01',param4:=92);

//删除存储过程

 DROP PROCEDURE insert_data;

自治事务：主事务的回滚，不会影响自治事务已经提交的内容

--建表
create table t2(a int, b int);
insert into t2 values(1,2);
select * from t2;

//--创建包含自治事务的存储过程
CREATE OR REPLACE PROCEDURE autonomous_4(a int, b int) AS
DECLARE
  num3 int := a;
  num4 int := b;
PRAGMA AUTONOMOUS_TRANSACTION;
BEGIN
  insert into t2 values(num3, num4);
END;
/

//--创建调用自治事务存储过程的普通存储过程
CREATE OR REPLACE PROCEDURE autonomous_5(a int, b int) AS
DECLARE
BEGIN
  insert into t2 values(666, 666);
  autonomous_4(a,b);
  rollback;
END;
/

//--调用普通存储过程
select autonomous_5(11,22);

//--查看表结果
select * from t2 order by a;



//匿名块支持自治事务：

create table t1(a int ,b text);

START TRANSACTION;
DECLARE
PRAGMA AUTONOMOUS_TRANSACTION;
BEGIN
  insert into t1 values(1,'you are so cute,will commit!');
END;
/

insert into t1 values(1,'you will rollback!');
rollback;

select * from t1;



//物化视图

物化视图实际上就是存储SQL执行语句的结果，可以直接使用数据而不用重复执行查询语句，从而提升性能。

//创建数据表：CREATE TABLE t1(c1 int, c2 int);

​							INSERT INTO t1 VALUES(1, 1);

​							INSERT INTO t1 VALUES(2, 2);

​	//创建全量物化视图：CREATE MATERIALIZED VIEW mv AS select count(*) from t1;

//查询物化视图结果:SELECT * FROM mv;

//向物化视图的基表中插入数据:INSERT INTO t1 VALUES(3, 3);

//对全量物化视图做全量刷新:REFRESH MATERIALIZED VIEW mv;

//查询物化视图结果：SELECT * FROM mv;

//删除物化视图:DROP MATERIALIZED VIEW mv;



数据库安装部署：一主多备

参考文档：看安装pdf

[安装open Gauss](https://docs.opengauss.org/zh/docs/5.0.0/docs/InstallationGuide/安装openGauss.html)

