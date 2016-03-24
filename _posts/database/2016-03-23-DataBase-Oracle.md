---
layout: post
title: Oracle常用函数大全
categories: [database, oracle]
description: Oracle常用函数大全
keywords: database, oracle
autotoc: true
comments: true
---

##一、Oralce函数

###1.	ASCII 
返回与指定的字符对应的十进制数; 

```sql
SQL> select ascii(A) A,ascii(a) a,ascii(0) zero,ascii( ) space from dual; 
A A ZERO SPACE 
--------------------------------------------------- 
65 97 48 32 
```
###2. CHR 
给出整数,返回对应的字符; 

```sql
SQL> select chr(54740) zhao,chr(65) chr65 from dual; 
ZH C 
--------------------------------------------------- 
赵 A 
```
###3. CONCAT 
连接两个字符串; 

```sql
SQL> select concat(010-,88888888)||转23 高乾竞电话 from dual; 
高乾竞电话 
--------------------------------------------------- 
010-88888888转23 
```

###4. INITCAP 
返回字符串并将字符串的第一个字母变为大写; 

```sql
SQL> select initcap(smith) upp from dual; 
UPP 
--------------------------------------------------- 
Smith 
```

###5.INSTR(C1,C2,I,J) 

在一个字符串中搜索指定的字符,返回发现指定的字符的位置; 
C1 被搜索的字符串 
C2 希望搜索的字符串 
I 搜索的开始位置,默认为1 
J 出现的位置,默认为1 

```sql
SQL> select instr(oracle traning,ra,1,2) instring from dual; 
INSTRING 
---------------------------------------------------
9 
```

###6.LENGTH 
返回字符串的长度; 

```sql
SQL> select name,length(name),addr,length(addr),sal,length(to_char(sal)) from gao.nchar_tst; 
NAME LENGTH(NAME) ADDR LENGTH(ADDR) SAL LENGTH(TO_CHAR(SAL)) 
---------------------------------------------------
高乾竞 3 北京市海锭区 6 9999.99 7 
```

###7.LOWER 
返回字符串,并将所有的字符小写 

```sql
SQL> select lower(AaBbCcDd)AaBbCcDd from dual; 
AABBCCDD 
---------------------------------------------------
aabbccdd 
```

###8.UPPER 
返回字符串,并将所有的字符大写 

```sql
SQL> select upper(AaBbCcDd) upper from dual; 
UPPER 
---------------------------------------------------
AABBCCDD 
```

###9.RPAD和LPAD(粘贴字符) 
RPAD 在列的右边粘贴字符 
LPAD 在列的左边粘贴字符 

```sql
SQL> select lpad(rpad(gao,10,*),17,*)from dual; 
LPAD(RPAD(GAO,1 
---------------------------------------------------
*******gao******* 
不够字符则用*来填满 
```

###10.LTRIM和RTRIM 
LTRIM 删除左边出现的字符串 
RTRIM 删除右边出现的字符串 

```sql
SQL> select ltrim(rtrim( gao qian jing , ), ) from dual; 
LTRIM(RTRIM( 
---------------------------------------------------
gao qian jing 
```

###11.SUBSTR(string,start,count) 
取子字符串,从start开始,取count个 

```sql
SQL> select substr(13088888888,3,8) from dual; 
SUBSTR( 
---------------------------------------------------
08888888 
```

###12.REPLACE(string,s1,s2) 
string 希望被替换的字符或变量 
s1 被替换的字符串 
s2 要替换的字符串 

```sql
SQL> select replace(he love you,he,i) from dual; 
REPLACE(H 
---------------------------------------------------
i love you 
```

###13.SOUNDEX 
返回一个与给定的字符串读音相同的字符串 

```sql
SQL> create table table1(xm varchar(8)); 
SQL> insert into table1 values(weather); 
SQL> insert into table1 values(wether); 
SQL> insert into table1 values(gao); 
SQL> select xm from table1 where soundex(xm)=soundex(weather); 
XM 
--------------------------------------------------- 
weather 
wether 
```

###14.TRIM(s from string) 

- LEADING 剪掉前面的字符 
- TRAILING 剪掉后面的字符 

如果不指定,默认为空格符 

###15.ABS 
返回指定值的绝对值 

```sql
SQL> select abs(100),abs(-100) from dual; 
ABS(100) ABS(-100) 
---------------------------------------------------
100 100 
```

###16.ACOS 
给出反余弦的值 

```sql
SQL> select acos(-1) from dual; 
ACOS(-1) 
---------------------------------------------------
3.1415927 
```

###17.ASIN 
给出反正弦的值 

```sql
SQL> select asin(0.5) from dual; 
ASIN(0.5) 
---------------------------------------------------
.52359878 
```

###18.ATAN 
返回一个数字的反正切值 

```sql
SQL> select atan(1) from dual; 
ATAN(1) 
--------------------------------------------------- 
.78539816 
```

###19.CEIL 
返回大于或等于给出数字的最小整数 

```sql
SQL> select ceil(3.1415927) from dual; 
CEIL(3.1415927) 
---------------------------------------------------
4 
```

###20.COS 
返回一个给定数字的余弦 

```sql
SQL> select cos(-3.1415927) from dual; 
COS(-3.1415927) 
--------------------------------------------------- 
-1
```

###21.COSH 
返回一个数字反余弦值 

```sql
SQL> select cosh(20) from dual; 
COSH(20) 
---------------------------------------------------
242582598 
```

###22.EXP 
返回一个数字e的n次方根 

```sql
SQL> select exp(2),exp(1) from dual; 
EXP(2) EXP(1) 
---------------------------------------------------
7.3890561 2.7182818 
```

###23.FLOOR 
对给定的数字取整数 

```sql
SQL> select floor(2345.67) from dual; 
FLOOR(2345.67) 
---------------------------------------------------
2345 
```

###24.LN 
返回一个数字的对数值 

```sql
SQL> select ln(1),ln(2),ln(2.7182818) from dual; 
LN(1) LN(2) LN(2.7182818) 
--------- --------- ------------- 
0 .69314718 .99999999 
```

###25.LOG(n1,n2) 
返回一个以n1为底n2的对数 

```sql
SQL> select log(2,1),log(2,4) from dual; 
LOG(2,1) LOG(2,4) 
--------- --------- 
0 2
``` 

###26.MOD(n1,n2) 
返回一个n1除以n2的余数 

```sql
SQL> select mod(10,3),mod(3,3),mod(2,3) from dual; 
MOD(10,3) MOD(3,3) MOD(2,3) 
--------- --------- --------- 
1 0 2 
```

###27.POWER 
返回n1的n2次方根 

```sql
SQL> select power(2,10),power(3,3) from dual; 
POWER(2,10) POWER(3,3) 
----------- ---------- 
1024 27 
```

###28.ROUND和TRUNC 
按照指定的精度进行舍入 

```sql
SQL> select round(55.5),round(-55.4),trunc(55.5),trunc(-55.5) from dual; 
ROUND(55.5) ROUND(-55.4) TRUNC(55.5) TRUNC(-55.5) 
----------- ------------ ----------- ------------ 
56 -55 55 -55 
```

###29.SIGN 
取数字n的符号,大于0返回1,小于0返回-1,等于0返回0 

```sql
SQL> select sign(123),sign(-100),sign(0) from dual; 
SIGN(123) SIGN(-100) SIGN(0) 
--------- ---------- --------- 
1 -1 0 
```

###30.SIN 
返回一个数字的正弦值 

```sql
SQL> select sin(1.57079) from dual; 
SIN(1.57079) 
------------ 
1 
```

###31.SIGH 

```sql
返回双曲正弦的值 
SQL> select sin(20),sinh(20) from dual; 
SIN(20) SINH(20) 
--------- --------- 
.91294525 242582598 
```

###32.SQRT 
返回数字n的根 

```sql
SQL> select sqrt(64),sqrt(10) from dual; 
SQRT(64) SQRT(10) 
--------- --------- 
8 3.1622777 
```

###33.TAN 
返回数字的正切值 

```sql
SQL> select tan(20),tan(10) from dual; 
TAN(20) TAN(10) 
--------- --------- 
2.2371609 .64836083 
```

###34.TANH 
返回数字n的双曲正切值 

```sql
SQL> select tanh(20),tan(20) from dual; 
TANH(20) TAN(20) 
--------- --------- 
1 2.2371609 
```

###35.TRUNC 
按照指定的精度截取一个数 

```sql
SQL> select trunc(124.1666,-2) trunc1,trunc(124.16666,2) from dual; 
TRUNC1 TRUNC(124.16666,2) 
--------- ------------------ 
100 124.16 
```

###36.ADD_MONTHS 
增加或减去月份

```sql 
SQL> select to_char(add_months(to_date(199912,yyyymm),2),yyyymm) from dual; 
TO_CHA 
------ 
200002 
SQL> select to_char(add_months(to_date(199912,yyyymm),-2),yyyymm) from dual; 
TO_CHA 
------ 
199910 
```

###37.LAST_DAY 
返回日期的最后一天 

```sql
SQL> select to_char(sysdate,yyyy.mm.dd),to_char((sysdate)+1,yyyy.mm.dd) from dual; 
TO_CHAR(SY TO_CHAR((S 
---------- ---------- 
2004.05.09 2004.05.10 
SQL> select last_day(sysdate) from dual; 
LAST_DAY(S 
---------- 
31-5月 -04 
```

###38.MONTHS_BETWEEN(date2,date1) 
给出date2-date1的月份 

```sql
SQL> select months_between(19-12月-1999,19-3月-1999) mon_between from dual; 
MON_BETWEEN 
----------- 
9 
SQL>selectmonths_between(to_date(2000.05.20,yyyy.mm.dd),to_date(2005.05.20,yyyy.mm.dd)) mon_betw from dual; 
MON_BETW 
--------- 
-60 
```

###39.NEW_TIME(date,this,that) 
给出在this时区=other时区的日期和时间 

```sql
SQL> select to_char(sysdate,yyyy.mm.dd hh24:mi:ss) bj_time,to_char(new_time 
2 (sysdate,PDT,GMT),yyyy.mm.dd hh24:mi:ss) los_angles from dual; 
BJ_TIME LOS_ANGLES 
------------------- ------------------- 
2004.05.09 11:05:32 2004.05.09 18:05:32 
```

###40.NEXT_DAY(date,day) 
给出日期date和星期x之后计算下一个星期的日期 

星期日 = 1  星期一 = 2  星期二 = 3 
星期三 = 4  星期四 = 5  星期五 = 6  星期六 = 7

```sql
SQL> select next_day('18-5月-2001','星期五') next_day from dual; 
NEXT_DAY 
---------- 
25-5月 -01 
```

###41.SYSDATE 
用来得到系统的当前日期 

```sql
SQL> select to_char(sysdate,dd-mm-yyyy day) from dual; 
TO_CHAR(SYSDATE, 
----------------- 
09-05-2004 星期日 
trunc(date,fmt)按照给出的要求将日期截断,如果fmt=mi表示保留分,截断秒 
SQL> select to_char(trunc(sysdate,hh),yyyy.mm.dd hh24:mi:ss) hh, 
2 to_char(trunc(sysdate,mi),yyyy.mm.dd hh24:mi:ss) hhmm from dual; 
HH HHMM 
------------------- ------------------- 
2004.05.09 11:00:00 2004.05.09 11:17:00 

```

###42.CHARTOROWID 
将字符数据类型转换为ROWID类型 

```sql
SQL> select rowid,rowidtochar(rowid),ename from scott.emp; 
ROWID ROWIDTOCHAR(ROWID) ENAME 
------------------ ------------------ ---------- 
AAAAfKAACAAAAEqAAA AAAAfKAACAAAAEqAAA SMITH 
AAAAfKAACAAAAEqAAB AAAAfKAACAAAAEqAAB ALLEN 
AAAAfKAACAAAAEqAAC AAAAfKAACAAAAEqAAC WARD 
AAAAfKAACAAAAEqAAD AAAAfKAACAAAAEqAAD JONES 

```

###43.CONVERT(c,dset,sset) 
将源字符串 sset从一个语言字符集转换到另一个目的dset字符集 

```sql
SQL> select convert(strutz,we8hp,f7dec) "conversion" from dual; 
conver 
------ 
strutz 
```

###44.HEXTORAW 
将一个十六进制构成的字符串转换为二进制 

###45.RAWTOHEXT 
将一个二进制构成的字符串转换为十六进制 

###46.ROWIDTOCHAR 
将ROWID数据类型转换为字符类型 

###47.TO_CHAR(date,format) 

```sql
SQL> select to_char(sysdate,yyyy/mm/dd hh24:mi:ss) from dual; 
TO_CHAR(SYSDATE,YY 
------------------- 
2004/05/09 21:14:41 
```

###48.TO_DATE(string,format) 
将字符串转化为ORACLE中的一个日期 

###49.TO_MULTI_BYTE 
将字符串中的单字节字符转化为多字节字符 

```sql
SQL> select to_multi_byte(高) from dual; 
TO 
-- 
高 
```

###50.TO_NUMBER 
将给出的字符转换为数字 

```sql
SQL> select to_number(1999) year from dual; 
YEAR 
--------- 
1999
```

###51.BFILENAME(dir,file) 
指定一个外部二进制文件 

```sql
SQL>insert into file_tb1 values(bfilename(lob_dir1,image1.gif)); 

```

###52.CONVERT(x,desc,source) 
将x字段或变量的源source转换为desc 

```sql
SQL> select sid,serial#,username,decode(command, 
2 0,none, 
3 2,insert, 
4 3, 
5 select, 
6 6,update, 
7 7,delete, 
8 8,drop, 
9 other) cmd from v$session where type!=background; 
SID SERIAL# USERNAME CMD 
--------- --------- ------------------------------ ------ 
1 1 none 
2 1 none 
3 1 none 
4 1 none 
5 1 none 
6 1 none 
7 1275 none 
8 1275 none 
9 20 GAO select 
10 40 GAO none 
```

###53.DUMP(s,fmt,start,length) 
DUMP函数以fmt指定的内部数字格式返回一个VARCHAR2类型的值 

```sql
SQL> col global_name for a30 
SQL> col dump_string for a50 
SQL> set lin 200 
SQL> select global_name,dump(global_name,1017,8,5) dump_string from global_name; 
GLOBAL_NAME DUMP_STRING 
------------------------------ -------------------------------------------------- 
ORACLE.WORLD Typ=1 Len=12 CharacterSet=ZHS16GBK: W,O,R,L,D 
```

###54.EMPTY_BLOB()和EMPTY_CLOB() 
这两个函数都是用来对大数据类型字段进行初始化操作的函数 

###55.GREATEST 
返回一组表达式中的最大值,即比较字符的编码大小. 
注意NULL要进行转0处理

```sql
SQL> select greatest(AA,AB,AC) from dual; 
GR 
-- 
AC 
SQL> select greatest(啊,安,天) from dual; 
GR 
-- 
天 
```

具体用法可参看<http://yedward.net/?id=142>，可以实现不同列之间的比较，或者同一列的所有数值比较

###56.LEAST 
返回一组表达式中的最小值 
注意NULL要进行转0处理

```sql
SQL> select least(啊,安,天) from dual; 
LE 
-- 
啊 
```

具体用法可参看<http://yedward.net/?id=142>，可以实现不同列之间的比较，或者同一列的所有数值比较

###57.UID 
返回标识当前用户的唯一整数

```sql
SQL> show user 
USER 为"GAO" 
SQL> select username,user_id from dba_users where user_id=uid; 
USERNAME USER_ID 
------------------------------ --------- 
GAO 25 
```

###58.USER 
返回当前用户的名字 

```sql
SQL> select user from dual; 
USER 
------------------------------ 
GAO 
```

###59.USEREVN 
返回当前用户环境的信息,opt可以是: 
ENTRYID,SESSIONID,TERMINAL,ISDBA,LABLE,LANGUAGE,CLIENT_INFO,LANG,VSIZE 
ISDBA 查看当前用户是否是DBA如果是则返回true 

```sql
SQL> select userenv(isdba) from dual; 
USEREN 
------ 
FALSE 
SQL> select userenv(isdba) from dual; 
USEREN 
------ 
TRUE 
SESSION 
返回会话标志 
SQL> select userenv(sessionid) from dual; 
USERENV(SESSIONID) 
-------------------- 
152 
ENTRYID 
返回会话人口标志 
SQL> select userenv(entryid) from dual; 
USERENV(ENTRYID) 
------------------ 
0 
INSTANCE 
返回当前INSTANCE的标志 
SQL> select userenv(instance) from dual; 
USERENV(INSTANCE) 
------------------- 
1 
LANGUAGE 
返回当前环境变量 
SQL> select userenv(language) from dual; 
USERENV(LANGUAGE) 
---------------------------------------------------- 
SIMPLIFIED CHINESE_CHINA.ZHS16GBK 
LANG 
返回当前环境的语言的缩写 
SQL> select userenv(lang) from dual; 
USERENV(LANG) 
---------------------------------------------------- 
ZHS 
TERMINAL 
返回用户的终端或机器的标志 
SQL> select userenv(terminal) from dual; 
USERENV(TERMINA 
---------------- 
GAO 
VSIZE(X) 
返回X的大小(字节)数 
SQL> select vsize(user),user from dual; 
VSIZE(USER) USER 
----------- ------------------------------ 
6 SYSTEM 
```

###60.AVG(DISTINCT|ALL) 
all表示对所有的值求平均值,distinct只对不同的值求平均值 

```sql
SQLWKS> create table table3(xm varchar(8),sal number(7,2)); 
语句已处理。 
SQLWKS> insert into table3 values(gao,1111.11); 
SQLWKS> insert into table3 values(gao,1111.11); 
SQLWKS> insert into table3 values(zhu,5555.55); 
SQLWKS> commit; 
SQL> select avg(distinct sal) from gao.table3; 
AVG(DISTINCTSAL) 
---------------- 
3333.33 
SQL> select avg(all sal) from gao.table3; 
AVG(ALLSAL) 
----------- 
2592.59 
```

###61.MAX(DISTINCT|ALL) 
求最大值,ALL表示对所有的值求最大值,DISTINCT表示对不同的值求最大值,相同的只取一次 

```sql
SQL> select max(distinct sal) from scott.emp; 
MAX(DISTINCTSAL) 
---------------- 
5000 
```

###62.MIN(DISTINCT|ALL) 
求最小值,ALL表示对所有的值求最小值,DISTINCT表示对不同的值求最小值,相同的只取一次 

```sql
SQL> select min(all sal) from gao.table3; 
MIN(ALLSAL) 
----------- 
1111.11 
```

###63.STDDEV(distinct|all) 
求标准差,ALL表示对所有的值求标准差,DISTINCT表示只对不同的值求标准差 

```sql
SQL> select stddev(sal) from scott.emp; 
STDDEV(SAL) 
----------- 
1182.5032 
SQL> select stddev(distinct sal) from scott.emp; 
STDDEV(DISTINCTSAL) 
------------------- 
1229.951 
```

###64.VARIANCE(DISTINCT|ALL) 
求协方差 

功能描述：该函数返回表达式的变量，Oracle计算该变量如下： 
如果表达式中行数为1，则返回0 
如果表达式中行数大于1，则返回VAR_SAMP 

```sql
SQL> select variance(sal) from scott.emp; 
VARIANCE(SAL) 
------------- 
1398313.9 
```

SAMPLE：下例返回部门30按雇佣日期排序的薪水值的累积变化 

```sql
SELECT last_name, salary, VARIANCE(salary) 
OVER (ORDER BY hire_date) "Variance" 
FROM employees 
WHERE department_id = 30; 
LAST_NAME SALARY Variance 
------------------------- ---------- ---------- 
Raphaely 11000 0 
Khoo 3100 31205000 
Tobias 2800 21623333.3 
Baida 2900 16283333.3 
Himuro 2600 13317000 
Colmenares 2500 11307000
```

###65.GROUP BY 
主要用来对一组数进行统计 

```sql
SQL> select deptno,count(*),sum(sal) from scott.emp group by deptno; 
DEPTNO COUNT(*) SUM(SAL) 
--------- --------- --------- 
10 3 8750 
20 5 10875 
30 6 9400 
```

###66.HAVING 
对分组统计再加限制条件 

```sql
SQL> select deptno,count(*),sum(sal) from scott.emp group by deptno having count(*)>=5; 
DEPTNO COUNT(*) SUM(SAL) 
--------- --------- --------- 
20 5 10875 
30 6 9400 
SQL> select deptno,count(*),sum(sal) from scott.emp having count(*)>=5 group by deptno ; 
DEPTNO COUNT(*) SUM(SAL) 
--------- --------- --------- 
20 5 10875 
30 6 9400 
```

###67.ORDER BY 
用于对查询到的结果进行排序输出 

```sql
SQL> select deptno,ename,sal from scott.emp order by deptno,sal desc; 
DEPTNO ENAME SAL 
--------- ---------- --------- 
10 KING 5000 
10 CLARK 2450 
10 MILLER 1300 
20 SCOTT 3000 
20 FORD 3000 
20 JONES 2975 
20 ADAMS 1100 
20 SMITH 800 
30 BLAKE 2850 
30 ALLEN 1600 
30 TURNER 1500 
30 WARD 1250 
30 MARTIN 1250 
30 JAMES 950 
```

###68. pl/sql中的case语句 

```sql
select  (case  when  DUMMY='X'  then  0  else  1  end)  as  flag  from  dual; 
```

- case的第1种用法： 
case col when 'a' then 1 
when 'b' then 2 
else 0 end 
这种用法跟decode一样没什么区别 
- case的第2种用法： 
case when score <60 then 'd' 
when score >=60 and score <70 then 'c' 
when score >=70 and score <80 then 'b' 
else 'a' end 


###69.NVL(expr1, expr2) 


- NVL(expr1, expr2)->expr1为NULL，返回expr2；不为NULL，返回expr1。注意两者的类型要一致 
- NVL2 (expr1, expr2, expr3) ->expr1不为NULL，返回expr2；为NULL，返回expr3。expr2和expr3类型不同的话，expr3会转换为expr2的类型 
- NULLIF (expr1, expr2) ->相等返回NULL，不等返回expr1 


###70.AVG 
功能描述：用于计算一个组和数据窗口内表达式的平均值。 
SAMPLE：下面的例子中列c_mavg计算员工表中每个员工的平均薪水报告，该平均值由当前员工和与之具有相同经理的前一个和后一个三者的平均数得来； 

```sql
SELECT manager_id, last_name, hire_date, salary, 
AVG(salary) OVER (PARTITION BY manager_id ORDER BY hire_date 
ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) AS c_mavg 
FROM employees; 
MANAGER_ID LAST_NAME HIRE_DATE SALARY C_MAVG 
---------- ------------------------- --------- ---------- ---------- 
100 Kochhar 21-SEP-89 17000 17000 
100 De Haan 13-JAN-93 17000 15000 
100 Raphaely 07-DEC-94 11000 11966.6667 
100 Kaufling 01-MAY-95 7900 10633.3333 
100 Hartstein 17-FEB-96 13000 9633.33333 
100 Weiss 18-JUL-96 8000 11666.6667 
100 Russell 01-OCT-96 14000 11833.3333 
```

###71.CORR 
功能描述：返回一对表达式的相关系数，它是如下的缩写： 

 COVAR_POP(expr1,expr2)/STDDEV_POP(expr1)*STDDEV_POP(expr2)) 

从统计上讲，相关性是变量之间关联的强度，变量之间的关联意味着在某种程度 
上一个变量的值可由其它的值进行预测。通过返回一个-1~1之间的一个数, 相关 
系数给出了关联的强度，0表示不相关。 

SAMPLE：下例返回1998年月销售收入和月单位销售的关系的累积系数（本例在SH用户下运行） 

```sql
SELECT t.calendar_month_number, 
CORR (SUM(s.amount_sold), SUM(s.quantity_sold)) 
OVER (ORDER BY t.calendar_month_number) as CUM_CORR 
FROM sales s, times t 
WHERE s.time_id = t.time_id AND calendar_year = 1998 
GROUP BY t.calendar_month_number 
ORDER BY t.calendar_month_number; 
CALENDAR_MONTH_NUMBER CUM_CORR 
--------------------- ---------- 
1 
2 1 
3 .994309382 
4 .852040875 
5 .846652204 
6 .871250628 
7 .910029803 
8 .917556399 
9 .920154356 
10 .86720251 
11 .844864765 
12 .903542662 
```

###72.COVAR_POP 
功能描述：返回一对表达式的总体协方差。 
SAMPLE：下例CUM_COVP返回定价和最小产品价格的累积总体协方差 

```sql
SELECT product_id, supplier_id, 
COVAR_POP(list_price, min_price) 
OVER (ORDER BY product_id, supplier_id) AS CUM_COVP, 
COVAR_SAMP(list_price, min_price) 
OVER (ORDER BY product_id, supplier_id) AS CUM_COVS 
FROM product_information p 
WHERE category_id = 29 
ORDER BY product_id, supplier_id; 
PRODUCT_ID SUPPLIER_ID CUM_COVP CUM_COVS 
---------- ----------- ---------- ---------- 
1774 103088 0 
1775 103087 1473.25 2946.5 
1794 103096 1702.77778 2554.16667 
1825 103093 1926.25 2568.33333 
2004 103086 1591.4 1989.25 
2005 103086 1512.5 1815 
2416 103088 1475.97959 1721.97619 
. 
. 
```

###73.COVAR_SAMP 
功能描述：返回一对表达式的样本协方差 
SAMPLE：下例CUM_COVS返回定价和最小产品价格的累积样本协方差 

```sql
SELECT product_id, supplier_id, 
COVAR_POP(list_price, min_price) 
OVER (ORDER BY product_id, supplier_id) AS CUM_COVP, 
COVAR_SAMP(list_price, min_price) 
OVER (ORDER BY product_id, supplier_id) AS CUM_COVS 
FROM product_information p 
WHERE category_id = 29 
ORDER BY product_id, supplier_id; 
PRODUCT_ID SUPPLIER_ID CUM_COVP CUM_COVS 
---------- ----------- ---------- ---------- 
1774 103088 0 
1775 103087 1473.25 2946.5 
1794 103096 1702.77778 2554.16667 
1825 103093 1926.25 2568.33333 
2004 103086 1591.4 1989.25 
2005 103086 1512.5 1815 
2416 103088 1475.97959 1721.97619 
```

###74.COUNT 
功能描述：对一组内发生的事情进行累积计数，如果指定*或一些非空常数，count将对所有行计数，如果指定一个表达式，count 
返回表达式非空赋值的计数，当有相同值出现时，这些相等的值都会被纳入被计算的值；可以使用DISTINCT来记录去掉一组中完全 
相同的数据后出现的行数。 
SAMPLE：下面例子中计算每个员工在按薪水排序中当前行附近薪水在[n-50,n+150]之间的行数，n表示当前行的薪水 
例如，Philtanker的薪水2200，排在他之前的行中薪水大于等于2200-50的有1行，排在他之后的行中薪水小于等于2200＋150的行 
没有，所以count计数值cnt3为2（包括自己当前行）；cnt2值相当于小于等于当前行的SALARY值的所有行数 

```sql
SELECT last_name, salary, COUNT(*) OVER () AS cnt1, 
COUNT(*) OVER (ORDER BY salary) AS cnt2, 
COUNT(*) OVER (ORDER BY salary RANGE BETWEEN 50 PRECEDING 
AND 150 FOLLOWING) AS cnt3 FROM employees; 
LAST_NAME SALARY CNT1 CNT2 CNT3 
------------------------- ---------- ---------- ---------- ---------- 
Olson 2100 107 1 3 
Markle 2200 107 3 2 
Philtanker 2200 107 3 2 
Landry 2400 107 5 8 
Gee 2400 107 5 8 
Colmenares 2500 107 11 10 
Patel 2500 107 11 10 
. 
. 
```

###75.CUME_DIST 
功能描述：计算一行在组中的相对位置，CUME_DIST总是返回大于0、小于或等于1的数，该数表示该行在N行中的位置。例如， 
在一个3行的组中，返回的累计分布值为1/3、2/3、3/3 
SAMPLE：下例中计算每个工种的员工按薪水排序依次累积出现的分布百分比 

```sql
SELECT job_id, last_name, salary, CUME_DIST() 
OVER (PARTITION BY job_id ORDER BY salary) AS cume_dist 
FROM employees WHERE job_id LIKE 'PU%'; 
JOB_ID LAST_NAME SALARY CUME_DIST 
---------- ------------------------- ---------- ---------- 
PU_CLERK Colmenares 2500 .2 
PU_CLERK Himuro 2600 .4 
PU_CLERK Tobias 2800 .6 
PU_CLERK Baida 2900 .8 
PU_CLERK Khoo 3100 1 
PU_MAN Raphaely 11000 1 
```

###76.DENSE_RANK 
功能描述：根据ORDER BY子句中表达式的值，从查询返回的每一行，计算它们与其它行的相对位置。组内的数据按ORDER BY子句排序，然后给每一行赋一个号，从而形成一个序列，该序列从1开始，往后累加。每次ORDER BY表达式的值发生变化时，该序列也随之增加。有同样值的行得到同样的数字序号（认为null时相等的）。密集的序列返回的时没有间隔的数 

SAMPLE：下例中计算每个员工按部门分区再按薪水排序，依次出现的序列号（注意与RANK函数的区别）

```sql 
SELECT d.department_id , e.last_name, e.salary, DENSE_RANK() 
OVER (PARTITION BY e.department_id ORDER BY e.salary) as drank 
FROM employees e, departments d 
WHERE e.department_id = d.department_id 
AND d.department_id IN ('60', '90'); 
DEPARTMENT_ID LAST_NAME SALARY DRANK 
------------- ------------------------- ---------- ---------- 
60 Lorentz 4200 1 
60 Austin 4800 2 
60 Pataballa 4800 2 
60 Ernst 6000 3 
60 Hunold 9000 4 
90 Kochhar 17000 1 
90 De Haan 17000 1 
90 King 24000 2 
```

###77.FIRST 
功能描述：从DENSE_RANK返回的集合中取出排在最前面的一个值的行（可能多行，因为值可能相等），因此完整的语法需要在开始处加上一个集合函数以从中取出记录 

SAMPLE：下面例子中DENSE_RANK按部门分区，再按佣金commission_pct排序，FIRST取出佣金最低的对应的所有行，然后前面的MAX函数从这个集合中取出薪水最低的值；LAST取出佣金最高的对应的所有行，然后前面的MIN函数从这个集合中取出薪水最高的值 

```sql
SELECT last_name, department_id, salary, 
MIN(salary) KEEP (DENSE_RANK FIRST ORDER BY commission_pct) 
OVER (PARTITION BY department_id) "Worst", 
MAX(salary) KEEP (DENSE_RANK LAST ORDER BY commission_pct) 
OVER (PARTITION BY department_id) "Best" 
FROM employees 
WHERE department_id in (20,80) 
ORDER BY department_id, salary; 
LAST_NAME DEPARTMENT_ID SALARY Worst Best 
------------------------- ------------- ---------- ---------- ---------- 
Fay 20 6000 6000 13000 
Hartstein 20 13000 6000 13000 
Kumar 80 6100 6100 14000 
Banda 80 6200 6100 14000 
Johnson 80 6200 6100 14000 
Ande 80 6400 6100 14000 
Lee 80 6800 6100 14000 
Tuvault 80 7000 6100 14000 
Sewall 80 7000 6100 14000 
Marvins 80 7200 6100 14000 
Bates 80 7300 6100 14000 
. 
. 
. 
```

###78.FIRST_VALUE 
功能描述：返回组中数据窗口的第一个值。 

SAMPLE：下面例子计算按部门分区按薪水排序的数据窗口的第一个值对应的名字，如果薪水的第一个值有多个，则从多个对应的名字中取缺省排序的第一个名字 

```sql
SELECT department_id, last_name, salary, FIRST_VALUE(last_name) 
OVER (PARTITION BY department_id ORDER BY salary ASC ) AS lowest_sal 
FROM employees 
WHERE department_id in(20,30); 
DEPARTMENT_ID LAST_NAME SALARY LOWEST_SAL 
------------- ------------------------- ---------- -------------- 
20 Fay 6000 Fay 
20 Hartstein 13000 Fay 
30 Colmenares 2500 Colmenares 
30 Himuro 2600 Colmenares 
30 Tobias 2800 Colmenares 
30 Baida 2900 Colmenares 
30 Khoo 3100 Colmenares 
30 Raphaely 11000 Colmenares 
```

###79.LAG 
功能描述：可以访问结果集中的其它行而不用进行自连接。它允许去处理游标，就好像游标是一个数组一样。在给定组中可参考当前行之前的行，这样就可以从组中与当前行一起选择以前的行。Offset是一个正整数，其默认值为1，若索引超出窗口的范围，就返回默认值（默认返回的是组中第一行），其相反的函数是**LEAD **

SAMPLE：下面的例子中列prev_sal返回按hire_date排序的前1行的salary值 

```sql
SELECT last_name, hire_date, salary, 
LAG(salary, 1, 0) OVER (ORDER BY hire_date) AS prev_sal 
FROM employees 
WHERE job_id = 'PU_CLERK'; 
LAST_NAME HIRE_DATE SALARY PREV_SAL 
------------------------- ---------- ---------- ---------- 
Khoo 18-5月 -95 3100 0 
Tobias 24-7月 -97 2800 3100 
Baida 24-12月-97 2900 2800 
Himuro 15-11月-98 2600 2900 
Colmenares 10-8月 -99 2500 2600
```

###80.LAST 
功能描述：从DENSE_RANK返回的集合中取出排在最后面的一个值的行（可能多行，因为值可能相等），因此完整的语法需要在开始处加上一个集合函数以从中取出记录 

SAMPLE：下面例子中DENSE_RANK按部门分区，再按佣金commission_pct排序，FIRST取出佣金最低的对应的所有行，然后前面的MAX函数从这个集合中取出薪水最低的值；LAST取出佣金最高的对应的所有行，然后前面的MIN函数从这个集合中取出薪水最高的值 

```sql
SELECT last_name, department_id, salary, 
MIN(salary) KEEP (DENSE_RANK FIRST ORDER BY commission_pct) 
OVER (PARTITION BY department_id) "Worst", 
MAX(salary) KEEP (DENSE_RANK LAST ORDER BY commission_pct) 
OVER (PARTITION BY department_id) "Best" 
FROM employees 
WHERE department_id in (20,80) 
ORDER BY department_id, salary; 
LAST_NAME DEPARTMENT_ID SALARY Worst Best 
------------------------- ------------- ---------- ---------- ---------- 
Fay 20 6000 6000 13000 
Hartstein 20 13000 6000 13000 
Kumar 80 6100 6100 14000 
Banda 80 6200 6100 14000 
Johnson 80 6200 6100 14000 
Ande 80 6400 6100 14000 
Lee 80 6800 6100 14000 
Tuvault 80 7000 6100 14000 
Sewall 80 7000 6100 14000 
Marvins 80 7200 6100 14000 
Bates 80 7300 6100 14000 
. 
```

###81.LAST_VALUE 
功能描述：返回组中数据窗口的最后一个值。 

SAMPLE：下面例子计算按部门分区按薪水排序的数据窗口的最后一个值对应的名字，如果薪水的最后一个值有多个，则从多个对应的名字中取缺省排序的最后一个名字 

```sql
SELECT department_id, last_name, salary, LAST_VALUE(last_name) 
OVER(PARTITION BY department_id ORDER BY salary) AS highest_sal 
FROM employees 
WHERE department_id in(20,30); 
DEPARTMENT_ID LAST_NAME SALARY HIGHEST_SAL 
------------- ------------------------- ---------- ------------ 
20 Fay 6000 Fay 
20 Hartstein 13000 Hartstein 
30 Colmenares 2500 Colmenares 
30 Himuro 2600 Himuro 
30 Tobias 2800 Tobias 
30 Baida 2900 Baida 
30 Khoo 3100 Khoo 
30 Raphaely 11000 Raphaely
```

###82.LEAD 
功能描述：LEAD与LAG相反，LEAD可以访问组中当前行之后的行。Offset是一个正整数，其默认值为1，若索引超出窗口的范围，就返回默认值（默认返回的是组中第一行） 

SAMPLE：下面的例子中每行的"NextHired"返回按hire_date排序的下一行的hire_date值 

```sql
SELECT last_name, hire_date, 
LEAD(hire_date, 1) OVER (ORDER BY hire_date) AS "NextHired" 
FROM employees WHERE department_id = 30; 
LAST_NAME HIRE_DATE NextHired 
------------------------- --------- --------- 
Raphaely 07-DEC-94 18-MAY-95 
Khoo 18-MAY-95 24-JUL-97 
Tobias 24-JUL-97 24-DEC-97 
Baida 24-DEC-97 15-NOV-98 
Himuro 15-NOV-98 10-AUG-99 
Colmenares 10-AUG-99 
```

###83.MAX 
功能描述：在一个组中的数据窗口中查找表达式的最大值。 

SAMPLE：下面例子中dept_max返回当前行所在部门的最大薪水值 

```sql
SELECT department_id, last_name, salary, 
MAX(salary) OVER (PARTITION BY department_id) AS dept_max 
FROM employees WHERE department_id in (10,20,30); 
DEPARTMENT_ID LAST_NAME SALARY DEPT_MAX 
------------- ------------------------- ---------- ---------- 
10 Whalen 4400 4400 
20 Hartstein 13000 13000 
20 Fay 6000 13000 
30 Raphaely 11000 11000 
30 Khoo 3100 11000 
30 Baida 2900 11000 
30 Tobias 2800 11000 
30 Himuro 2600 11000 
30 Colmenares 2500 11000 
```

###84.MIN 
功能描述：在一个组中的数据窗口中查找表达式的最小值。 

SAMPLE：下面例子中dept_min返回当前行所在部门的最小薪水值 

```sql
SELECT department_id, last_name, salary, 
MIN(salary) OVER (PARTITION BY department_id) AS dept_min 
FROM employees WHERE department_id in (10,20,30); 
DEPARTMENT_ID LAST_NAME SALARY DEPT_MIN 
------------- ------------------------- ---------- ---------- 
10 Whalen 4400 4400 
20 Hartstein 13000 6000 
20 Fay 6000 6000 
30 Raphaely 11000 2500 
30 Khoo 3100 2500 
30 Baida 2900 2500 
30 Tobias 2800 2500 
30 Himuro 2600 2500 
30 Colmenares 2500 2500 
```

###85.NTILE 
功能描述：将一个组分为"表达式"的散列表示，例如，如果表达式=4，则给组中的每一行分配一个数（从1到4），如果组中有20行，则给前5行分配1，给下5行分配2等等。如果组的基数不能由表达式值平均分开，则对这些行进行分配时，组中就没有任何percentile的行数比其它percentile的行数超过一行，最低的percentile是那些拥有额外行的percentile。例如，若表达式=4，行数=21，则percentile=1的有5行，percentile=2的有5行等等。 

SAMPLE：下例中把6行数据分为4份 

```sql
SELECT last_name, salary, 
NTILE(4) OVER (ORDER BY salary DESC) AS quartile FROM employees 
WHERE department_id = 100; 
LAST_NAME SALARY QUARTILE 
------------------------- ---------- ---------- 
Greenberg 12000 1 
Faviet 9000 1 
Chen 8200 2 
Urman 7800 2 
Sciarra 7700 3 
Popp 6900 4 
```

###86.PERCENT_RANK 
功能描述：和CUME_DIST（累积分配）函数类似，对于一个组中给定的行来说，在计算那行的序号时，先减1，然后除以n-1（n为组中所有的行数）。该函数总是返回0～1（包括1）之间的数。 
SAMPLE：下例中如果Khoo的salary为2900，则pr值为0.6，因为RANK函数对于等值的返回序列值是一样的 

```sql
SELECT department_id, last_name, salary, 
PERCENT_RANK() 
OVER (PARTITION BY department_id ORDER BY salary) AS pr 
FROM employees 
WHERE department_id < 50 
ORDER BY department_id,salary; 
DEPARTMENT_ID LAST_NAME SALARY PR 
------------- ------------------------- ---------- ---------- 
10 Whalen 4400 0 
20 Fay 6000 0 
20 Hartstein 13000 1 
30 Colmenares 2500 0 
30 Himuro 2600 0.2 
30 Tobias 2800 0.4 
30 Baida 2900 0.6 
30 Khoo 3100 0.8 
30 Raphaely 11000 1 
40 Mavris 6500 0 
```

###87.PERCENTILE_CONT 
功能描述：返回一个与输入的分布百分比值相对应的数据值，分布百分比的计算方法见函数PERCENT_RANK，如果没有正好对应的数据值，就通过下面算法来得到值： 
RN = 1+ (P*(N-1)) 其中P是输入的分布百分比值，N是组内的行数 
CRN = CEIL(RN) FRN = FLOOR(RN) 
if (CRN = FRN = RN) then 
(value of expression from row at RN) 
else 
(CRN - RN) * (value of expression for row at FRN) + 
(RN - FRN) * (value of expression for row at CRN) 
注意：本函数与PERCENTILE_DISC的区别在找不到对应的分布值时返回的替代值的计算方法不同 
SAMPLE：在下例中，对于部门60的Percentile_Cont值计算如下： 
P=0.7 N=5 RN =1+ (P*(N-1)=1+(0.7*(5-1))=3.8 CRN = CEIL(3.8)=4 
FRN = FLOOR(3.8)=3 
（4 - 3.8）* 4800 + (3.8 - 3) * 6000 = 5760 

```sql
SELECT last_name, salary, department_id, 
PERCENTILE_CONT(0.7) WITHIN GROUP (ORDER BY salary) 
OVER (PARTITION BY department_id) "Percentile_Cont", 
PERCENT_RANK() 
OVER (PARTITION BY department_id ORDER BY salary) "Percent_Rank" 
FROM employees WHERE department_id IN (30, 60); 
LAST_NAME SALARY DEPARTMENT_ID Percentile_Cont Percent_Rank 
------------------------- ---------- ------------- --------------- ------------ 
Colmenares 2500 30 3000 0 
Himuro 2600 30 3000 0.2 
Tobias 2800 30 3000 0.4 
Baida 2900 30 3000 0.6 
Khoo 3100 30 3000 0.8 
Raphaely 11000 30 3000 1 
Lorentz 4200 60 5760 0 
Austin 4800 60 5760 0.25 
Pataballa 4800 60 5760 0.25 
Ernst 6000 60 5760 0.75 
Hunold 9000 60 5760 1 
```

###88.PERCENTILE_DISC 
功能描述：返回一个与输入的分布百分比值相对应的数据值，分布百分比的计算方法见函数CUME_DIST，如果没有正好对应的数据值，就取大于该分布值的下一个值。 
注意：本函数与PERCENTILE_CONT的区别在找不到对应的分布值时返回的替代值的计算方法不同 
SAMPLE：下例中0.7的分布值在部门30中没有对应的Cume_Dist值，所以就取下一个分布值0.83333333所对应的SALARY来替代 

```sql
SELECT last_name, salary, department_id, 
PERCENTILE_DISC(0.7) WITHIN GROUP (ORDER BY salary ) 
OVER (PARTITION BY department_id) "Percentile_Disc", 
CUME_DIST() OVER (PARTITION BY department_id ORDER BY salary) "Cume_Dist" 
FROM employees 
WHERE department_id in (30, 60); 
LAST_NAME SALARY DEPARTMENT_ID Percentile_Disc Cume_Dist 
------------------------- ---------- ------------- --------------- ---------- 
Colmenares 2500 30 3100 .166666667 
Himuro 2600 30 3100 .333333333 
Tobias 2800 30 3100 .5 
Baida 2900 30 3100 .666666667 
Khoo 3100 30 3100 .833333333 
Raphaely 11000 30 3100 1 
Lorentz 4200 60 6000 .2 
Austin 4800 60 6000 .6 
Pataballa 4800 60 6000 .6 
Ernst 6000 60 6000 .8 
Hunold 9000 60 6000 1 
```
###89.RANK 
功能描述：根据ORDER BY子句中表达式的值，从查询返回的每一行，计算它们与其它行的相对位置。组内的数据按ORDER BY子句排序， 
然后给每一行赋一个号，从而形成一个序列，该序列从1开始，往后累加。每次ORDER BY表达式的值发生变化时，该序列也随之增加。 
有同样值的行得到同样的数字序号（认为null时相等的）。然而，如果两行的确得到同样的排序，则序数将随后跳跃。若两行序数为1， 
则没有序数2，序列将给组中的下一行分配值3，DENSE_RANK则没有任何跳跃。 
SAMPLE：下例中计算每个员工按部门分区再按薪水排序，依次出现的序列号（注意与DENSE_RANK函数的区别） 

```sql
SELECT d.department_id , e.last_name, e.salary, RANK() 
OVER (PARTITION BY e.department_id ORDER BY e.salary) as drank 
FROM employees e, departments d 
WHERE e.department_id = d.department_id 
AND d.department_id IN ('60', '90'); 
DEPARTMENT_ID LAST_NAME SALARY DRANK 
------------- ------------------------- ---------- ---------- 
60 Lorentz 4200 1 
60 Austin 4800 2 
60 Pataballa 4800 2 
60 Ernst 6000 4 
60 Hunold 9000 5 
90 Kochhar 17000 1 
90 De Haan 17000 1 
90 King 24000 3
```

与DENSE_RANK区别具体参看：<http://www.linuxidc.com/Linux/2015-04/116349.htm>

###90.RATIO_TO_REPORT 
功能描述：该函数计算expression/(sum(expression))的值，它给出相对于总数的百分比，即当前行对sum(expression)的贡献。 
SAMPLE：下例计算每个员工的工资占该类员工总工资的百分比 

```sql
SELECT last_name, salary, RATIO_TO_REPORT(salary) OVER () AS rr 
FROM employees 
WHERE job_id = 'PU_CLERK'; 
LAST_NAME SALARY RR 
------------------------- ---------- ---------- 
Khoo 3100 .223021583 
Baida 2900 .208633094 
Tobias 2800 .201438849 
Himuro 2600 .18705036 
Colmenares 2500 .179856115 
```

###91.REGR_ (Linear Regression) Functions 
功能描述：这些线性回归函数适合最小二乘法回归线，有9个不同的回归函数可使用。 
- REGR_SLOPE：返回斜率，等于COVAR_POP(expr1, expr2) / VAR_POP(expr2) 
- REGR_INTERCEPT：返回回归线的y截距，等于 
AVG(expr1) - REGR_SLOPE(expr1, expr2) * AVG(expr2) 
- REGR_COUNT：返回用于填充回归线的非空数字对的数目 
- REGR_R2：返回回归线的决定系数，计算式为： 
If VAR_POP(expr2) = 0 then return NULL 
If VAR_POP(expr1) = 0 and VAR_POP(expr2) != 0 then return 1 
If VAR_POP(expr1) > 0 and VAR_POP(expr2 != 0 then 
return POWER(CORR(expr1,expr),2) 
- REGR_AVGX：计算回归线的自变量(expr2)的平均值，去掉了空对(expr1, expr2)后，等于AVG(expr2) 
- REGR_AVGY：计算回归线的应变量(expr1)的平均值，去掉了空对(expr1, expr2)后，等于AVG(expr1) 
- REGR_SXX： 返回值等于REGR_COUNT(expr1, expr2) * VAR_POP(expr2) 
- REGR_SYY： 返回值等于REGR_COUNT(expr1, expr2) * VAR_POP(expr1) 
- REGR_SXY: 返回值等于REGR_COUNT(expr1, expr2) * COVAR_POP(expr1, expr2) 
（下面的例子都是在SH用户下完成的） 
SAMPLE 1：下例计算1998年最后三个星期中两种产品（260和270）在周末的销售量中已开发票数量和总数量的累积斜率和回归线的截距 

```sql
SELECT t.fiscal_month_number "Month", t.day_number_in_month "Day", 
REGR_SLOPE(s.amount_sold, s.quantity_sold) 
OVER (ORDER BY t.fiscal_month_desc, t.day_number_in_month) AS CUM_SLOPE, 
REGR_INTERCEPT(s.amount_sold, s.quantity_sold) 
OVER (ORDER BY t.fiscal_month_desc, t.day_number_in_month) AS CUM_ICPT 
FROM sales s, times t 
WHERE s.time_id = t.time_id 
AND s.prod_id IN (270, 260) 
AND t.fiscal_year=1998 
AND t.fiscal_week_number IN (50, 51, 52) 
AND t.day_number_in_week IN (6,7) 
ORDER BY t.fiscal_month_desc, t.day_number_in_month; 
Month Day CUM_SLOPE CUM_ICPT 
---------- ---------- ---------- ---------- 
12 12 -68 1872 
12 12 -68 1872 
12 13 -20.244898 1254.36735 
12 13 -20.244898 1254.36735 
12 19 -18.826087 1287 
12 20 62.4561404 125.28655 
12 20 62.4561404 125.28655 
12 20 62.4561404 125.28655 
12 20 62.4561404 125.28655 
12 26 67.2658228 58.9712313 
12 26 67.2658228 58.9712313 
12 27 37.5245541 284.958221 
12 27 37.5245541 284.958221 
12 27 37.5245541 284.958221 
```

SAMPLE 2：下例计算1998年4月每天的累积交易数量 

```sql
SELECT UNIQUE t.day_number_in_month, 
REGR_COUNT(s.amount_sold, s.quantity_sold) 
OVER (PARTITION BY t.fiscal_month_number ORDER BY t.day_number_in_month) 
"Regr_Count" 
FROM sales s, times t 
WHERE s.time_id = t.time_id 
AND t.fiscal_year = 1998 AND t.fiscal_month_number = 4; 
DAY_NUMBER_IN_MONTH Regr_Count 
------------------- ---------- 
1 825 
2 1650 
3 2475 
4 3300
26 21450 
30 22200 
```

SAMPLE 3：下例计算1998年每月销售量中已开发票数量和总数量的累积回归线决定系数 

```sql
SELECT t.fiscal_month_number, 
REGR_R2(SUM(s.amount_sold), SUM(s.quantity_sold)) 
OVER (ORDER BY t.fiscal_month_number) "Regr_R2" 
FROM sales s, times t 
WHERE s.time_id = t.time_id 
AND t.fiscal_year = 1998 
GROUP BY t.fiscal_month_number 
ORDER BY t.fiscal_month_number; 
FISCAL_MONTH_NUMBER Regr_R2 
------------------- ---------- 
1 
2 1 
3 .927372984 
4 .807019972 
5 .932745567 
6 .94682861 
7 .965342011 
8 .955768075 
9 .959542618 
10 .938618575 
11 .880931415 
12 .882769189 
```

SAMPLE 4：下例计算1998年12月最后两周产品260的销售量中已开发票数量和总数量的累积平均值 

```sql
SELECT t.day_number_in_month, 
REGR_AVGY(s.amount_sold, s.quantity_sold) 
OVER (ORDER BY t.fiscal_month_desc, t.day_number_in_month) 
"Regr_AvgY", 
REGR_AVGX(s.amount_sold, s.quantity_sold) 
OVER (ORDER BY t.fiscal_month_desc, t.day_number_in_month) 
"Regr_AvgX" 
FROM sales s, times t 
WHERE s.time_id = t.time_id 
AND s.prod_id = 260 
AND t.fiscal_month_desc = '1998-12' 
AND t.fiscal_week_number IN (51, 52) 
ORDER BY t.day_number_in_month; 
DAY_NUMBER_IN_MONTH Regr_AvgY Regr_AvgX 
------------------- ---------- ---------- 
14 882 24.5 
14 882 24.5 
15 801 22.25 
15 801 22.25 
16 777.6 21.6 
18 642.857143 17.8571429 
18 642.857143 17.8571429 
20 589.5 16.375 
21 544 15.1111111 
22 592.363636 16.4545455 
22 592.363636 16.4545455 
24 553.846154 15.3846154 
24 553.846154 15.3846154 
26 522 14.5 
27 578.4 16.0666667 
```

SAMPLE 5：下例计算产品260和270在1998年2月周末销售量中已开发票数量和总数量的累积REGR_SXY, REGR_SXX, and REGR_SYY统计值 

```sql
SELECT t.day_number_in_month, 
REGR_SXY(s.amount_sold, s.quantity_sold) 
OVER (ORDER BY t.fiscal_year, t.fiscal_month_desc) "Regr_sxy", 
REGR_SYY(s.amount_sold, s.quantity_sold) 
OVER (ORDER BY t.fiscal_year, t.fiscal_month_desc) "Regr_syy", 
REGR_SXX(s.amount_sold, s.quantity_sold) 
OVER (ORDER BY t.fiscal_year, t.fiscal_month_desc) "Regr_sxx" 
FROM sales s, times t 
WHERE s.time_id = t.time_id 
AND prod_id IN (270, 260) 
AND t.fiscal_month_desc = '1998-02' 
AND t.day_number_in_week IN (6,7) 
ORDER BY t.day_number_in_month; 
DAY_NUMBER_IN_MONTH Regr_sxy Regr_syy Regr_sxx 
------------------- ---------- ---------- ---------- 
1 18870.4 2116198.4 258.4 
1 18870.4 2116198.4 258.4 
1 18870.4 2116198.4 258.4 
1 18870.4 2116198.4 258.4 
7 18870.4 2116198.4 258.4 
8 18870.4 2116198.4 258.4 
14 18870.4 2116198.4 258.4 
15 18870.4 2116198.4 258.4 
21 18870.4 2116198.4 258.4 
22 18870.4 2116198.4 258.4
```

###92.ROW_NUMBER 
功能描述：返回有序组中一行的偏移量，从而可用于按特定标准排序的行号。 

SAMPLE：下例返回每个员工再在每个部门中按员工号排序后的顺序号 

```sql
SELECT department_id, last_name, employee_id, ROW_NUMBER() 
OVER (PARTITION BY department_id ORDER BY employee_id) AS emp_id 
FROM employees 
WHERE department_id < 50; 
DEPARTMENT_ID LAST_NAME EMPLOYEE_ID EMP_ID 
------------- ------------------------- ----------- ---------- 
10 Whalen 200 1 
20 Hartstein 201 1 
20 Fay 202 2 
30 Raphaely 114 1 
30 Khoo 115 2 
30 Baida 116 3 
30 Tobias 117 4 
30 Himuro 118 5 
30 Colmenares 119 6 
40 Mavris 203 1 
```

###93.STDDEV 
功能描述：计算当前行关于组的标准偏离。（Standard Deviation） 
SAMPLE：下例返回部门30按雇佣日期排序的薪水值的累积标准偏离 

```sql
SELECT last_name, hire_date,salary, 
STDDEV(salary) OVER (ORDER BY hire_date) "StdDev" 
FROM employees 
WHERE department_id = 30; 
LAST_NAME HIRE_DATE SALARY StdDev 
------------------------- ---------- ---------- ---------- 
Raphaely 07-12月-94 11000 0 
Khoo 18-5月 -95 3100 5586.14357 
Tobias 24-7月 -97 2800 4650.0896 
Baida 24-12月-97 2900 4035.26125 
Himuro 15-11月-98 2600 3649.2465 
Colmenares 10-8月 -99 2500 3362.58829 
```

###94.STDDEV_POP 
功能描述：该函数计算总体标准偏离，并返回总体变量的平方根，其返回值与VAR_POP函数的平方根相同。（Standard Deviation－Population） 
SAMPLE：下例返回部门20、30、60的薪水值的总体标准偏差 

```sql
SELECT department_id, last_name, salary, 
STDDEV_POP(salary) OVER (PARTITION BY department_id) AS pop_std 
FROM employees 
WHERE department_id in (20,30,60); 
DEPARTMENT_ID LAST_NAME SALARY POP_STD 
------------- ------------------------- ---------- ---------- 
20 Hartstein 13000 3500 
20 Fay 6000 3500 
30 Raphaely 11000 3069.6091 
30 Khoo 3100 3069.6091 
30 Baida 2900 3069.6091 
30 Colmenares 2500 3069.6091 
30 Himuro 2600 3069.6091 
30 Tobias 2800 3069.6091 
60 Hunold 9000 1722.32401 
60 Ernst 6000 1722.32401 
60 Austin 4800 1722.32401 
60 Pataballa 4800 1722.32401 
60 Lorentz 4200 1722.32401
```

###95.STDDEV_SAMP 
功能描述： 该函数计算累积样本标准偏离，并返回总体变量的平方根，其返回值与VAR_POP函数的平方根相同。（Standard Deviation－Sample） 
SAMPLE：下例返回部门20、30、60的薪水值的样本标准偏差 

```sql
SELECT department_id, last_name, hire_date, salary, 
STDDEV_SAMP(salary) OVER 
(PARTITION BY department_id ORDER BY hire_date 
ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS cum_sdev 
FROM employees 
WHERE department_id in (20,30,60); 
DEPARTMENT_ID LAST_NAME HIRE_DATE SALARY CUM_SDEV 
------------- ------------------------- ---------- ---------- ---------- 
20 Hartstein 17-2月 -96 13000 
20 Fay 17-8月 -97 6000 4949.74747 
30 Raphaely 07-12月-94 11000 
30 Khoo 18-5月 -95 3100 5586.14357 
30 Tobias 24-7月 -97 2800 4650.0896 
30 Baida 24-12月-97 2900 4035.26125 
30 Himuro 15-11月-98 2600 3649.2465 
30 Colmenares 10-8月 -99 2500 3362.58829 
60 Hunold 03-1月 -90 9000 
60 Ernst 21-5月 -91 6000 2121.32034 
60 Austin 25-6月 -97 4800 2163.33077 
60 Pataballa 05-2月 -98 4800 1982.42276 
60 Lorentz 07-2月 -99 4200 1925.61678 
```

###96.SUM 
功能描述：该函数计算组中表达式的累积和。 
SAMPLE：下例计算同一经理下员工的薪水累积值 

```sql
SELECT manager_id, last_name, salary, 
SUM (salary) OVER (PARTITION BY manager_id ORDER BY salary 
RANGE UNBOUNDED PRECEDING) l_csum 
FROM employees 
WHERE manager_id in (101,103,108); 
MANAGER_ID LAST_NAME SALARY L_CSUM 
---------- ------------------------- ---------- ---------- 
101 Whalen 4400 4400 
101 Mavris 6500 10900 
101 Baer 10000 20900 
101 Greenberg 12000 44900 
101 Higgins 12000 44900 
103 Lorentz 4200 4200 
103 Austin 4800 13800 
103 Pataballa 4800 13800 
103 Ernst 6000 19800 
108 Popp 6900 6900 
108 Sciarra 7700 14600 
108 Urman 7800 22400 
108 Chen 8200 30600 
108 Faviet 9000 39600 
```

###97.VAR_POP 
功能描述：（Variance Population）该函数返回非空集合的总体变量（忽略null），VAR_POP进行如下计算： 
(SUM(expr2) - SUM(expr)2 / COUNT(expr)) / COUNT(expr) 
SAMPLE：下例计算1998年每月销售的累积总体和样本变量（本例在SH用户下运行） 

```sql
SELECT t.calendar_month_desc, 
VAR_POP(SUM(s.amount_sold)) 
OVER (ORDER BY t.calendar_month_desc) "Var_Pop", 
VAR_SAMP(SUM(s.amount_sold)) 
OVER (ORDER BY t.calendar_month_desc) "Var_Samp" 
FROM sales s, times t 
WHERE s.time_id = t.time_id AND t.calendar_year = 1998 
GROUP BY t.calendar_month_desc; 
CALENDAR Var_Pop Var_Samp 
-------- ---------- ---------- 
1998-01 0 
1998-02 6.1321E+11 1.2264E+12 
1998-03 4.7058E+11 7.0587E+11 
1998-04 4.6929E+11 6.2572E+11 
1998-05 1.5524E+12 1.9405E+12 
1998-06 2.3711E+12 2.8453E+12 
1998-07 3.7464E+12 4.3708E+12 
1998-08 3.7852E+12 4.3260E+12 
1998-09 3.5753E+12 4.0222E+12 
1998-10 3.4343E+12 3.8159E+12 
1998-11 3.4245E+12 3.7669E+12 
1998-12 4.8937E+12 5.3386E+12 
```

###98.VAR_SAMP 
功能描述：（Variance Sample）该函数返回非空集合的样本变量（忽略null），VAR_POP进行如下计算： 
(SUM(expr*expr)-SUM(expr)*SUM(expr)/COUNT(expr))/(COUNT(expr)-1) 
SAMPLE：下例计算1998年每月销售的累积总体和样本变量 

```sql
SELECT t.calendar_month_desc, 
VAR_POP(SUM(s.amount_sold)) 
OVER (ORDER BY t.calendar_month_desc) "Var_Pop", 
VAR_SAMP(SUM(s.amount_sold)) 
OVER (ORDER BY t.calendar_month_desc) "Var_Samp" 
FROM sales s, times t 
WHERE s.time_id = t.time_id AND t.calendar_year = 1998 
GROUP BY t.calendar_month_desc; 
CALENDAR Var_Pop Var_Samp 
-------- ---------- ---------- 
1998-01 0 
1998-02 6.1321E+11 1.2264E+12 
1998-03 4.7058E+11 7.0587E+11 
1998-04 4.6929E+11 6.2572E+11 
1998-05 1.5524E+12 1.9405E+12 
1998-06 2.3711E+12 2.8453E+12 
1998-07 3.7464E+12 4.3708E+12 
1998-08 3.7852E+12 4.3260E+12 
1998-09 3.5753E+12 4.0222E+12 
1998-10 3.4343E+12 3.8159E+12 
1998-11 3.4245E+12 3.7669E+12 
1998-12 4.8937E+12 5.3386E+12 
```



