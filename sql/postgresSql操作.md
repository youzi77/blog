# postgres操作

## 命令行语句

```bash

\h：查看SQL命令的解释，比如\h select。

\?：查看psql命令列表。

\l：列出所有数据库。

\c [database_name]：连接其他数据库。

\d：列出当前数据库的所有表格。

\d [table_name]：列出某一张表格的结构。

\du：列出所有用户。

\e：打开文本编辑器。

\conninfo：列出当前数据库和连接的信息。
```

## 相关sql语句

- ### 查询表大小

    ``` postgressql
    SELECT table_schema || '.' || table_name AS table_full_name, pg_size_pretty(pg_total_relation_size('"' || table_schema || '"."' || table_name || '"')) AS size FROM information_schema.tables ORDER BY pg_total_relation_size('"' || table_schema || '"."' || table_name || '"') DESC limit 20;
    ```

- ### 清除某个表中的数据
  
    ```sql
    TRUNCATE table_name; 
    ```

