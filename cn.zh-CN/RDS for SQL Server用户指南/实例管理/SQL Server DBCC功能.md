# SQL Server DBCC功能 {#concept_e5g_jmn_wdb .concept}

RDS for SQL Server 2012及以上版本支持DBCC的部分功能，您只需要使用存储过程`sp_rds_dbcc_trace`指定需要打开的跟踪标记即可。另外，您可以使用`DBCC tracestatus(-1)`查看跟踪标记是否被打开。

## 支持的跟踪标记 {#section_ut1_431_1fb .section}

-   1222
-   1204
-   1117
-   1118
-   1211
-   1224
-   3604

## 使用方法 {#section_orf_p31_1fb .section}

执行如下命令，即可使用DBCC功能：

```

USE master
GO
--database engine edtion
SELECT SERVERPROPERTY('edition')
GO
--create database
CREATE DATABASE testdb
GO
DBCC tracestatus(-1)
exec sp_rds_dbcc_trace 1222,1
WAITFOR DELAY '00:00:10'
DBCC tracestatus(-1)
GO
```

