- 当天

select * from 表名 where to_char(to_date(字段名,'yyyy-mm-dd hh24:mi:ss'),'dd')=to_char(sysdate,'dd') 
- 当周

select * from 表名 where to_char(to_date(字段名,'yyyy-mm-dd hh24:mi:ss'),'iw')=to_char(sysdate,'iw') 
- 当月

select * from 表名 where to_char(to_date(字段名,'yyyy-mm-dd hh24:mi:ss'),'mm')=to_char(sysdate,'mm') 
- 当季度

select * from 表名 where to_char(to_date(字段名,'yyyy-mm-dd hh24:mi:ss'),'q')=to_char(sysdate,'q') 
- 字段类型为date
- 当天

select * from 表名 where to_char(字段名,'dd')=to_char(sysdate,'dd') 
- 当周

select * from 表名 where to_char(字段名,'iw')=to_char(sysdate,'iw') 
- 当月

select * from 表名 where to_char(字段名,'mm')=to_char(sysdate,'mm') 

- 当季度

select * from 表名 where to_char(字段名,'q')=to_char(sysdate,'q') 

# oracle数据库导入
```
连接
sqlplus /nolog
conn / as sysdba

step2：创建表空间
create tablespace DMPTEST datafile 'D:\oracle\oradata\DMPTEST.DBF' size 50m  autoextend on
-- 然后通过navicat创建用户
-- 然后授权
grant connect,resource to DMPTEST;
grant dba to DMPTEST;
grant unlimited tablespace to DMPTEST;
grant create database link to DMPTEST;
grant select any sequence,create materialized view to DMPTEST;
-- 恢复库
create or replace directory data as 'D:\oracle\oradata\orcl';

grant read,write on directory data to DMPTEST;

impdp DMPTEST/DMPTEST directory=data dumpfile=DMPTEST_816.dmp exclude=statistics
-- 导入的时候不导入表统计信息，在导入完成后统计对库进行统计信息收集
execute dbms_stats.gather_schema_stats(ownname=>'DMPTEST');


mvn install:install-file -Dfile=D:\用户目录\下载\jaxb-core-2.1.14.jar -DgroupId=com.sun.xml.bind -DartifactId=jaxb-core -Dversion=2.1.14 -Dpackaging=jar  
mvn install:install-file -Dfile=D:\用户目录\下载\jaxb-impl-2.1.4.jar -DgroupId=com.sun.xml.bind -DartifactId=jaxb-impl -Dversion=2.1.14 -Dpackaging=jar 

create tablespaces zhongsy datafile '/home/oracle/zhongsy/zhongsy.DBF' size 50m  autoextend on ;
create user zhongsy identified by zhongsy default tablespaces zhongsy;
grant connect,resource to zhongsy;
grant dba to zhongsy;
grant unlimited tablespaces to zhongsy;
grant create database link to zhongsy;
grant select any sequence,create materialized view to zhongsy;
create directory dmp_dir as '/home/oracle/zhongsy'
grant read,write on directory dmp_dir to zhongsy
expdp zhongsy/zhongsy tablespaces=ZHONGSY dumpfile=expdp.dmp directory=dmp_dir ;

-- 陕汽创建表空间
CREATE TABLESPACE SQ_DMP DATAFILE '/oradata/SQMALL/datafile/SQ_DMP.dbf' SIZE 500M EXTENT MANAGEMENT LOCAL AUTOALLOCATE; 创建表空间
alter database datafile '/oradata/SQMALL/datafile/SQ_DMP.dbf' autoextend on; 设为自动增长
--创建用户 SQ_DMP ，密码 sq_mall_jerei_2018
CREATE USER SQ_DMP PROFILE DEFAULT IDENTIFIED BY sq_dmp_jerei_2019 DEFAULT TABLESPACE SQ_DMP TEMPORARY TABLESPACE TEMP ACCOUNT UNLOCK;
--为用户授权
grant connect,resource to SQ_DMP;
grant dba to SQ_DMP;
grant unlimited tablespace to SQ_DMP;
grant create database link to SQ_DMP;
grant select any sequence,create materialized view to SQ_DMP;
-- 创建dump目录 (创建恢复目录并授权)
create or replace directory data as '/home/oracle/sqdmp_db_back';
grant read,write on directory data to SQ_DMP;
-- 导入的时候不导入表统计信息，在导入完成后统计对库进行统计信息收集
-- DUMPFILE 要从中导入的转储文件的列表 [expdat.dmp];exclude:排除特定对象类型;参数 TRANSFORM=segment_attributes:n 去掉表空间和存储子句
-- FULL导入源中的所有对象 [Y];LOGFILE 指定日志名称;TABLE_EXISTS_ACTION导入对象已存在时执行的操作(APPEND, REPLACE, [SKIP] ,TRUNCATE)
impdp SQ_DMP/sq_dmp_jerei_2019 directory=data dumpfile=DMPTEST_1104.dmp REMAP_SCHEMA=DMPTEST:SQ_DMP FULL=y TABLE_EXISTS_ACTION=REPLACE exclude=USER TRANSFORM=segment_attributes:n
-- 导入之后统计信息
execute dbms_stats.gather_schema_stats(ownname=>'SQ_DMP');


-删除空的表空间，但是不包含物理文件
drop tablespace tablespace_name;
--删除非空表空间，但是不包含物理文件
drop tablespace tablespace_name including contents;
--删除空表空间，包含物理文件
drop tablespace tablespace_name including datafiles;
--删除非空表空间，包含物理文件
drop tablespace tablespace_name including contents and datafiles;
--如果其他表空间中的表有外键等约束关联到了本表空间中的表的字段，就要加上CASCADE CONSTRAINTS
drop tablespace tablespace_name including contents and datafiles CASCADE CONSTRAINTS;
--删除表空间
drop tablespace SQ_DMP including contents and datafiles cascade constraint;

-- 导出
创建导出空间
SQL> create or replace directory expdir as 'd:\exp';
  
Directory created
授权
SQL> grant read,write on directory expdir to public;
  
Grant succeeded
  
SQL> select * from dba_directories;


expdp "'sys/sys as sysdba'" schemas=SQ_DMP directory=expdir exclude=statistics dumpfile=SQ_DMP.dmp logfile=sqdmp.log 

	
expdp SQ_DMP/sq_dmp_jerei_2019@SQMALL dumpfile=SQ_DMP.dmp tablespaces=SQ_DMP logfile=exp.log directory=expdir job_name=my_job