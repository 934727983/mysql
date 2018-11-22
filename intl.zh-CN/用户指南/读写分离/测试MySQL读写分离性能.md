# 测试MySQL读写分离性能 {#concept_mfj_hqp_wdb .concept}

开通读写分离功能后，事务会默认全部路由至主实例上执行。本文将以常用的MySQL压测工具Sysbench 0.5版本为例，介绍如何正确配置其参数来进行读写分离性能的测试。

## 前提条件 {#section_x4z_pqp_wdb .section}

-   已开通读写分离功能。详细步骤请参见[开通读写分离](intl.zh-CN/用户指南/读写分离/开通读写分离.md#)。
-   已安装压测工具Sysbench 0.5。下载地址及安装步骤，请参见[Sysbench的官方文档](https://github.com/akopytov/sysbench/tree/0.5)。

## 注意事项 {#section_cv2_rqp_wdb .section}

-   建议测试读写分离的负载均衡不要用带prepare或者带事务的case。
-   避免因写压力过大而造成的主从延迟时间超过设定的监控检查阈值。
-   推荐使用如下Sysbench脚本，您可以实际情况构造具体的SQL。

    ```
    function thread_init(thread_id)
          db_connect()
      end
      function event(thread_id)
          rs =  db_query("select 1")
      end
    ```


## 设置Sysbench的参数 {#section_hnt_tqp_wdb .section}

Sysbench oltp.lua脚本测试默认使用事务，若使用默认参数，所有SQL都会在事务中执行，即使是只读SQL也会全部路由至主实例执行。所以，使用Sysbench压测读写分离的性能时，必须根据需求设置Sysbench的参数。例如，您可以通过设置`oltp-skip-trx`参数可以使Sysbench运行SQL时不在事务中执行。

## 设置常用参数 {#section_hql_wqp_wdb .section}

请根据您的实际业务情况，设置如下参数值。

|名称|描述|
|--|--|
|test|指定测试文件路径。|
|mysql-host|MySQL服务器地址。|
|mysql-port|MySQL服务器端口。|
|mysql-user|用户名。|
|mysql-password|密码。|
|mysql-db|测试使用数据库，需提前创建。|
|oltp-tables-count|建立表的个数。|
|oltp-table-size|每个表产生的记录数量。|
|rand-init|是否随机初始化数据。|
|max-time|压测持续时间。|
|max-requests|压测期间请求总数。|
|num-threads|并发线程数量。|
|report-interval|运行日志打印间隔。|

## 设置事务及读写SQL相关参数 {#section_d2h_yqp_wdb .section}

如下参数会影响事务及读写SQL，在进行读写分离性能测试时按照实际需求设置参数值。

|名称|描述|
|--|--|
|oltp-test-mode|测试类型，但在Sysbench 0.5版本中此参数没有生效，可以忽略。可选参数值如下：-   complex：默认值，事务测试。
-   simple：简单只读SQL测试。
-   nontrx：非事务测试。
-   sp：存储过程。

|
|oltp-skip-trx|是否跳过SQL语句开头的begin和结尾的commit。可选参数值如下：-   off：默认值，执行的SQL全部在事务中。
-   on：非事务模式，若执行连续的对比压测，需要重新准备数据（prepare）和清除数据（cleanup）。

**说明：** 在压测读写分离性能时，参数值需选择on，SQL语句前后不需要begin/commint。

|
|oltp-read-only|是否产生只读SQL。可选参数值如下：-   off：默认值，执行oltp.lua的读写混合SQL。
-   on：只产生只读SQL，不会产生update、delete和insert类型的SQL。

**说明：** 请根据需求选择参数值，进行只读或读写测试。

|

## 压测示例 {#section_wwh_prp_wdb .section}

**测试读写性能**

1.  执行如下命令，准备数据。

    ```
    sysbench --test=./tests/db/oltp.lua --mysql-host=127.0.0.1 --mysql-port=3001 --mysql-user=abc --mysql-password=abc123456 --mysql-db=testdb --oltp-tables-count=10 --oltp-table-size=500000 --report-interval=5 --oltp-skip-trx=on --oltp-read-only=off --rand-init=on --max-requests=0 --max-time=300 --num-threads=100 prepare;
    ```

2.  执行如下命令，运行测试。

    **说明：** 非事务的读写测试更新数据时容易出现类似`ALERT: Error 1062 Duplicate entry 'xxx' for key 'PRIMARY'`的错误，所以需要增加参数`--mysql-ignore-errors=1062`来跳过这个错误。若参数`mysql-ignore-errors`没有生效，则说明Sysbench版本较低，需将其升级至最新的0.5版本。

    ```
    sysbench --test=./tests/db/oltp.lua --mysql-host=127.0.0.1 --mysql-port=3001 --mysql-user=abc --mysql-password=abc123456 --mysql-db=testdb --oltp-tables-count=10 --oltp-table-size=500000 --report-interval=5 --oltp-skip-trx=on --oltp-read-only=off --mysql-ignore-errors=1062 --rand-init=on --max-requests=0 --max-time=300 --num-threads=100 run;
    ```

3.  执行如下命令，清除数据。

    ```
    sysbench --test=./tests/db/oltp.lua --mysql-host=127.0.0.1 --mysql-port=3001 --mysql-user=abc --mysql-password=abc123456 --mysql-db=testdb --oltp-tables-count=10 --oltp-table-size=500000 --report-interval=5 --oltp-skip-trx=on --oltp-read-only=off --rand-init=on --max-requests=0 --max-time=300 --num-threads=100 cleanup;
    ```


**测试只读性能**

1.  执行如下命令，准备数据。

    ```
    sysbench --test=./tests/db/oltp.lua --mysql-host=127.0.0.1 --mysql-port=3001 --mysql-user=abc --mysql-password=abc123456 --mysql-db=testdb --oltp-tables-count=10 --oltp-table-size=500000 --report-interval=5 --oltp-skip-trx=on --oltp-read-only=on --rand-init=on --max-requests=0 --max-time=300 --num-threads=100 prepare;
    ```

2.  执行如下命令，运行测试。

    ```
    sysbench --test=./tests/db/oltp.lua --mysql-host=127.0.0.1 --mysql-port=3001 --mysql-user=abc --mysql-password=abc123456 --mysql-db=testdb --oltp-tables-count=10 --oltp-table-size=500000 --report-interval=5 --oltp-skip-trx=on --oltp-read-only=on --rand-init=on --max-requests=0 --max-time=300 --num-threads=100 run;
    ```

3.  执行如下命令，清除数据。

    ```
    sysbench --test=./tests/db/oltp.lua --mysql-host=127.0.0.1 --mysql-port=3001 --mysql-user=abc --mysql-password=abc123456 --mysql-db=testdb --oltp-tables-count=10 --oltp-table-size=500000 --report-interval=5 --oltp-skip-trx=on --oltp-read-only=on --rand-init=on --max-requests=0 --max-time=300 --num-threads=100 cleanup;
    ```


