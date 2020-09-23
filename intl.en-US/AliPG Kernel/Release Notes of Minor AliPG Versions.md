# Release Notes of Minor AliPG Versions

This topic describes the release notes of minor AliPG versions.

## PostgreSQL 12

20200830

-   New features:
    -   The Ganos plug-in is upgraded to version 3.0.
    -   The SQL firewall plug-in is supported. It allows you to prevent the injection of malicious SQL statements.
    -   The bigm plug-in is supported. It allows you to implement fuzzy match.
    -   The TimescaleDB plug-in is supported. It allows you to process time series data.
-   Bugs fixed:
    -   The bug that prevents the backend from identifying the `rds_` prefix in parameters is fixed.
    -   The bug that prevents you from properly creating and using the pg\_cron plug-in is fixed.
    -   The bug that prevents you from loading the RDKit plug-in due to the lack of the required dependencies is fixed.

20200421

-   New features:
    -   AliPG is upgraded to ensure compatibility with PostgreSQL 12.2 of the Community edition.
    -   The Ganos plug-in is upgraded to version 2.7.
    -   The HLL plug-in of version 2.14 is supported.
    -   The PL/Proxy plug-in of version 2.9.0 is supported.
    -   The tsm\_system\_rows plug-in of version 1.0 is supported.
    -   The tsm\_system\_time plug-in of version 1.0 is supported.
    -   The smlar plug-in of version 1.0 is supported.
    -   The tds\_fdw plug-in of version 1.0 is supported.
-   Bug fixed:

    The bug that causes RDS instances to restart due to logical subscription timeout is fixed.


20200221

-   New features:
    -   The reservation for a specific number of connections is supported for the rds\_superuser role. If you are authorized with the rds\_superuser role, you can connect to an RDS instance to troubleshoot issues even if the number of established connections with the RDS instance reaches the upper limit.
    -   The wal2json plug-in is supported. For more information, see [Use the wal2json plug-in](/intl.en-US/AliPG Kernel/Use the wal2json plug-in.md).
    -   The Ganos plug-in is upgraded to version 2.6.
-   Bug fixed:

    Some permission issues are fixed.


20191230

New features:

-   The pg\_roaringbitmap, RDKit, mysql\_fdw, and Ganos plug-ins are supported. For more information about the pg\_roaringbitmap and mysql\_fdw plug-ins, see [Use the roaringbitmap plug-in](/intl.en-US/AliPG Kernel/Query vertical industry-specific data/Use the roaringbitmap plug-in.md) and [Use mysql\_fdw to read and write data to a MySQL database](/intl.en-US/AliPG Kernel/Run cross-database queries/Use mysql_fdw to read and write data to a MySQL database.md).
-   The permissions to publish all tables at a time and create subscriptions are granted to privileged accounts.

## PostgreSQL 11

20200830

-   New features:
    -   The Ganos plug-in is upgraded to version 3.0.
    -   The SQL firewall plug-in is supported. It allows you to prevent the injection of malicious SQL statements.
    -   The bigm plug-in is supported. It allows you to implement fuzzy match.
    -   The ZomboDB plug-in is supported. It allows you to implement text search and analysis.
-   Bugs fixed:
    -   The bug that prevents the backend from identifying the `rds_` prefix in parameters is fixed.
    -   The bug that causes a secondary RDS instance to exit because the failover slot has the same name as the streaming replication slot is fixed.
    -   The bug that prevents you from properly creating and using the pg\_cron plug-in is fixed.
    -   The bug in a global variable of the PASE plug-in is fixed.

20200610

New features:

-   The TimescaleDB plug-in of version 1.7.1 is supported. For more information, see [Use the TimescaleDB plug-in](/intl.en-US/AliPG Kernel/Query vertical industry-specific data/Use the TimescaleDB plug-in.md).
-   The pageinspect plug-in is supported for the rds\_superuser role.
-   The rds\_superuser role is authorized to grant the replication permissions to other users.

20200511

-   New feature:

    The Ganos plug-in is upgraded to version 2.8.

-   Bug fixed:

    The bug that causes the PASE plug-in to slowly run INSERT statements on HNSW indexes is fixed.


20200421

New features:

-   The failover slot function is supported. For more information, see [Failover slot](/intl.en-US/AliPG Kernel/Failover slot.md).
-   The PL/Proxy plug-in of version 2.9.0 is supported.
-   The tsm\_system\_rows plug-in of version 1.0 is supported.
-   The tsm\_system\_time plug-in of version 1.0 is supported.
-   The smlar plug-in of version 1.0 is supported.

20200402

-   New features:
    -   The HLL plug-in of version 2.14 is supported. It allows ApsaraDB RDS for PostgreSQL to support the HLL data type, respond to queries in milliseconds, and analyze approximate data at low costs but fast speeds. For example, you can query page views \(PVs\) and unique visitors \(UVs\) in real time and determine whether the analyzed approximate data contains the specified characteristic tags.
    -   The oss\_fdw plug-in of version 1.1 is supported. It allows you to store infrequently requested historical data to Object Storage Service \(OSS\) buckets. This reduces storage costs.
    -   The tds\_fdw plug-in of version 2.0.1 is supported. It allows you to initiate requests on an ApsaraDB RDS for PostgreSQL instance to query data from a Sybase or SQL Server database without extract, transform and load \(ETL\) operations. It also allows you to migrate data between an ApsaraDB RDS for PostgreSQL instance and a Sybase or SQL Server database.
-   Upgraded plug-ins
    -   The Ganos plug-in is upgraded to version 2.7.
    -   The wal2json plug-in is upgraded to version 2.2.
-   Performance optimization:

    The `shutdown -m fast` command is optimized.


20191218

New features:

-   The PASE plug-in is supported. It allows you to create indexes that are used to recognize images. For more information, see [Use the PASE plug-in](/intl.en-US/AliPG Kernel/Query vertical industry-specific data/Use the PASE plug-in.md).
-   The permissions to publish all tables at a time and create subscriptions are granted to privileged accounts.

## PostgreSQL 10

20200830

-   New features:
    -   The Ganos plug-in is upgraded to version 3.0.
    -   The SQL firewall plug-in is supported. It allows you to prevent the injection of malicious SQL statements.
    -   The bigm plug-in is supported. It allows you to implement fuzzy match.
-   Bug fixed:

    The bug that prevents you from properly creating and using the pg\_cron plug-in is fixed.


20200212

-   New features:
    -   The reservation for a specific number of connections is supported for the rds\_superuser role. If you are authorized with the rds\_superuser role, you can connect to an RDS instance to troubleshoot issues even if the number of established connections with the RDS instance reaches the upper limit.
    -   The Ganos plug-in is upgraded to version 2.6.
-   Bug fixed:

    The bug that causes long waits in streaming replication is fixed.


20190703

-   New features:
    -   AliPG is upgraded to version 10.9.
    -   The change from synchronous replication to asynchronous replication is supported when the ongoing replication times out.
-   Bug fixed:
    -   The bug that prevents you from properly creating the pg\_hint\_plan plug-in is fixed.
    -   The bug that causes failures in external RUM indexing is fixed.

## PostgreSQL 9.4

20200623

-   New features:
    -   The wal2json plug-in is upgraded to version 2.2.
    -   The xml2 plug-in of version 1.0 is supported.
-   Bug fixed:

    The bug that causes memory exhaustion when the wal2json plug-in runs is fixed.


20200210

The reservation for a specific number of connections is supported for the rds\_superuser role. If you are authorized with the rds\_superuser role, you can connect to an RDS instance to troubleshoot issues even if the number of established connections with the RDS instance reaches the upper limit.

20190601

AliPG is upgraded to version 9.4.19.

