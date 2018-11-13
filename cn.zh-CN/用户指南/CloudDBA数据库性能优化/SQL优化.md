# SQL优化 {#concept_vtg_lc4_wdb .concept}

CloudDBA可以根据您输入的SQL语句，提出优化建议。您也可以直接在CloudDBA服务中登录数据库，并使用SQL命令进行插入和管理数据的操作。本文将介绍如何使用CloudDBA优化和执行SQL语句。

**说明：** 目前仅如下版本实例支持此功能：

-   MySQL 5.7 高可用版
-   MySQL 5.6版
-   MySQL 5.5 高可用版
-   PostgreSQL 10.0版
-   PPAS 10.0版

## 操作步骤 {#section_imr_rc4_wdb .section}

1.  登录[RDS管理控制台](https://rds.console.aliyun.com/)。
2.  选择目标实例所在地域。单击目标实例ID，进入基本信息页面。
3.  在左侧导航栏中，选择**CloudDBA** \> **SQL优化** ，进入SQL优化页面。

4.  单击**登录数据库**，如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7910/15421018403072_zh-CN.png)

5.  填写登录信息，单击**登录**，如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7910/15421018403073_zh-CN.png)

    **参数说明：**

    |参数名称|说明|
    |----|--|
    |用户名|已授权登录数据库的账号名称。|
    |密码|登录数据库所用账号对应的密码。|

6.  选择要查询或管理的数据库，如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7910/15421018413074_zh-CN.png)

7.  在输入框中填写SQL语句。
8.  若您同时输入了多条SQL语句，选中一条目标语句，然后选择进行如下操作：

    **说明：** SQL操作中提供的所有功能都不支持批量操作。

    -   单击**查看执行计划**，即可在执行结果中查看SQL语句具体的执行计划。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7910/154210184131538_zh-CN.png)

    -   单击**SQL优化诊断**，系统会对所输入的SQL语句进行诊断并给出优化建议，如索引优化。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7910/15421018413076_zh-CN.png)

        **说明：** PostgreSQL、PPAS暂不支持SQL优化诊断。

    -   单击**执行语句**并选择返回行数，即可在已选数据库中执行SQL命令，可在**执行结果**中查看SQL执行结果。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7910/154210184131539_zh-CN.png)

    -   单击**格式优化**，系统会自动优化所输入SQL语句的格式。

    -   单击**撤销**，可以撤销上一步对SQL语句进行的修改。若您误撤销了上一步的操作，可以立刻单击**重做**，即可恢复被撤销的修改。

9.  若您需要查看SQL操作的执行历史，选择**执行历史**标签页即可。

