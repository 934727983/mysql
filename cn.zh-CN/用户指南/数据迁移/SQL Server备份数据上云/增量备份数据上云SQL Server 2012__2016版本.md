# 增量备份数据上云SQL Server 2012/2016版本 {#concept_bsg_zcw_ydb .concept}

## 适用场景 {#section_ntt_5dw_ydb .section}

RDS for SQL Server 2012/2016版提供了增量上云功能，将上云期间的业务中断时间控制在分钟级别，大大缩短了业务中断时间。RDS for SQL Server增量数据上云适用于以下场景：

-   基于备份文件物理迁移至RDS for SQL Server，而不是逻辑迁移。

**说明：** 

-   物理迁移是指基于文件的迁移，逻辑迁移是指将数据生成DML语句写入RDS for SQL Server。
-   物理迁移可做到数据库迁移后和本地环境100%一致。逻辑迁移无法做到100%一致，比如，索引碎片率和统计信息等。
-   若您对业务停止时间敏感，需要控制在分钟级别，建议选择增量迁移。

**说明：** 

如果您对业务停止时间不敏感（如2小时），当数据库小于100G时，建议您直接使用[全量备份数据上云SQL Server 2012/2016版本](intl.zh-CN/用户指南/数据迁移/SQL Server备份数据上云/全量备份数据上云SQL Server 2012__2016版本.md#)。


本文档旨在介绍基于用户OSS空间上的完全备份文件加上日志备份（或者差异备份文件），实现用户线下SQL Server数据库增量迁移到RDS for SQL Server。

## 操作流程举例 {#section_psh_d2w_ydb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7996/15439031856187_zh-CN.png)

根据上图增量上云案例，按时间维度，解释如下：

|上云阶段|步骤|说明|
|----|--|--|
|全量阶段|Step1. 00:00之前|完成准备工作，包括：-   完成DBCC CheckDB检查；
-   关闭本地环境备份系统；
-   修改数据库为FULL恢复模式。

|
|Step2. 00:01|用户开始对线下数据库做FULL Backup。|
|Step3. 02:00|完成FULL Backup，耗时近1小时，开始上传备份文件到OSS Bucket。|
|Step4. 03:00|完成备份文件上传，耗时1小时，开始在RDS控制台恢复FULL Backup文件。|
|Step5. 22:00|完成FULL Backup恢复上云，耗时19小时，开始数据库增量LOG备份上云过程。|
|增量阶段|Step6. 22:20|完成LOG备份并上传至OSS，耗时20分钟，开始在RDS控制台恢复增量LOG文件。|
|Step7. 22:30| -   完成LOG Backup上云，耗时10分钟。
-   重复Step6 – 7，不断Backup LOG、上传到OSS、增量上云LOG备份文件，确保最后一个Backup LOG文件尽量小（500MB以下）。
-   **停止本地应用对数据库的写入操作，再做一个LOG Backup，最后一次增量上云。**

 |
|打开数据库|Step8. 22:34|完成了最后一个LOG Backup文件增量上云操作，耗时4分钟，开始将数据库带上线。|
|Step9. 22:35|数据库上线完毕，如果选择异步执行DBCC操作，上线速度快，耗时1分钟。|

**从整个的动作流程和时间轴来看，用户需要停止应用的时间非常的短，仅在最后一个LOG Backup之前停止应用写入即可。**在本例中整个应用停止的时间控制在5分钟内。

## 前提条件 {#section_bdr_f2w_ydb .section}

-   **要求RDS for SQL Server为以下版本：**
    -   RDS for SQL Server 2012/2016 Web版、2012企业单机基础版。
    -   RDS for SQL Server 2012/2016 标准版、企业版高可用系列（即双机版）。
-   **授权RDS服务账号访问OSS权限**

    授予RDS服务账号访问OSS的权限后，系统会在访问控制RAM的角色管理中创建名为**AliyunRDSImportRole**的角色。请勿修改或删除这个角色，否则会导致上云时无法下载备份文件。如果修改或删除了这个角色，您需要通过数据上云向导重新授权。

-   **准备OSS Bucket**

    创建与目标实例同地域的OSS Bucket。如果Bucket已经存在，请跳过本步骤。创建方法请参考[创建存储空间](https://www.alibabacloud.com/help/zh/doc-detail/31885.htm?spm=a2c63.p38356.a3.4.40b84251jSxsqH)。

-   **确保数据库恢复模式为FULL**

    增量备份数据上云时，用户数据库的恢复模式必须是FULL模式。恢复模式是Simple模式时，不允许做事务日志备份，而差异备份文件有可能会很大，导致增量上云的时间被拉长。

-   **RDS for SQL Server空间要求**

    请确保RDS for SQL Server有足够的存储空间，如果空间不足，请提前升级实例空间，以免因空间不足而导致迁入失败。

-   **RDS for SQL Server中不能存在同名的目标数据库**

    如果同名的数据库已经存在，请先备份该数据库，再删除该数据库，最后创建迁移任务。

-   **在RDS for SQL Server上创建高权限账号**

    通过RDS控制台创建目标实例的高权限账号，如果已经存在高权限账号，请跳过本步骤。

-   **关闭本地备份系统**

    为确保增量上云成功，请关闭本地环境的备份系统。否则，可能会因为本地环境的备份系统对上云数据库的自动备份操作，导致增量上云失败。

-   **运行DBCC CHECKDB**

    在本地环境对需要上云的数据库做DBCC CHECKDB\(‘xxx’\)检查，执行完毕后，确保没有任何的allocation errors和consistency errors。正常的结果如下：

    ```
    CHECKDB found 0 allocation errors and 0 consistency errors in database 'xxx'.
      DBCC execution completed. If DBCC printed error messages, contact your system administrator.
    ```

    如果发现DBCC CHECKDB有任何错误，请先在本地环境修复数据库，否则会导致上云失败。


## 限制条件 {#section_pdf_l2w_ydb .section}

-   **备份文件版本**

    不支持由高版本的备份文件往低版本做迁移，比如：从SQL Server 2016迁移到RDS for SQL Server 2012等。

-   **备份文件后缀名限制**

    备份文件名仅支持bak、diff、trn或者log为后缀名。如果没有使用本文中的脚本生成备份文件，请使用如下后缀名：

    -   bak：表示全量备份文件
    -   diff：表示差异备份文件
    -   trn或者log：表示事务日志备份
-   **备份文件命名限制**

    数据库备份文件名中不能包含中文、@或者|等特殊字符，或者OSS Bucket中目录包含中文，会导致OSS备份数据恢复上云任务失败。


## 视频演示 {#section_mwg_5fw_ydb .section}

## 备份本地数据库 {#section_hfj_vzw_b2b .section}

**说明：** 在对本地数据库做全量备份之前，请确保本地环境的备份系统已经关闭。

1.  下载[备份脚本](https://rdshelpattachments.oss-cn-beijing.aliyuncs.com/Migration/RDSBackupSpecifiedDatabasesToLocal.sql)，用SSMS打开备份脚本。
2.  根据实际情况，修改如下4个参数：

    |配置项|说明|
    |---|--|
    |@backup\_databases\_list|需要备份的数据库，多个数据库以分号或者逗号分隔。|
    |@backup\_type|备份类型。参数值如下：    -   FULL：全量备份；
    -   DIFF：差异备份；
    -   LOG：日志备份。
|
    |@backup\_folder|备份文件所在的本地目录。如不存在，会自动创建。|
    |@is\_run|是否执行备份。参数值如下：    -   1：执行备份；
    -   0：只做检查，不执行备份。
|

3.  执行备份脚本。

## 上传备份文件到OSS {#section_agb_p1x_b2b .section}

本地数据库备份完成后，需要将备份文件上传到用户自己的OSS Bucket中。

-   方法一：使用ossbrowser工具上传

    推荐使用ossbrowser工具上传备份文件到OSS，具体请参考 [ossbrowser](https://www.alibabacloud.com/help/zh/doc-detail/61872.htm)。

-   方法二：使用OSS控制台上传

    如果备份文件小于5GB，直接使用OSS控制台上传。具体请参考[使用OSS控制台上传](https://www.alibabacloud.com/help/zh/doc-detail/31886.htm)。

-   方法三：使用OSS API上传

    如果您有全自动无人干预的上云需求，请使用OSS OpenAPI，通过断点续传的方式上传备份文件到OSS Bucket，具体请参考[断点续传](https://www.alibabacloud.com/help/zh/doc-detail/31850.htm)。


## 创建数据上云任务 {#section_tmd_xfk_zww .section}

1.  登录[RDS控制台](https://rdsnew.console.aliyun.com/)。
2.  选择目标实例所在地域。
3.  单击目标实例的ID，进入基本信息页面。
4.  在左侧菜单栏中选择**备份恢复**。
5.  单击右上角**OSS备份数据恢复上云**。
6.  如果您是第一次使用OSS备份数据恢复上云功能，需要给RDS官方服务账号授予访问OSS的权限：

    1.  单击数据导入向导第三项数据导入页面中的授权地址，如下图所示：

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7996/15439031856199_zh-CN.png)

    2.  跳转到RAM授权页面，请单击**同意授权**，完成授权。
     

7.  授权完毕后，在数据导入向导第三步数据导入页面设置如下参数，单击**确认**生成OSS备份数据上云任务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7998/15439031856114_zh-CN.png)

    |配置项|说明|
    |---|--|
    |数据库名|目标实例上的目标数据库名。|
    |OSS Bucket|选择备份文件所在的OSS Bucket。|
    |OSS子文件夹名|备份文件所在的子文件夹名字。|
    |OSS文件列表|单击右侧放大镜按钮，可以按照备份文件名前缀模糊查找，会展示文件名、文件大小和更新时间。请选择需要上云的备份文件。|
    |上云方案|     -   打开数据库（只有一个全量备份文件）：全量上云，指用户仅有一个完全备份文件上云RDS for SQL Server的场景。本操作选择**打开数据库**。
    -   不打开数据库（还有差异备份或日志文件）：增量上云，用户有全量备份文件和差异或者日志备份文件，增量上云RDS for SQL Server的场景。
 |
    |一致性检查方式|     -   异步执行DBCC：在打开数据库的时候系统不做DBCC CheckDB，会在打开数据库任务结束以后，异步执行DBCC CheckDB操作，以此来节约打开数据库操作的时间开销（数据库比较大，DBCC CheckDB非常耗时），减少用户的业务停机时间。如果，您对业务停机时间要求非常敏感，且不关心DBCC CheckDB结果，建议使用异步执行DBCC。
    -   同步执行DBCC：相对于异步执行DBCC，有的用户非常关心DBCC CheckDB的结果，以此来找出用户线下数据库数据一致性错误。此时，建议您选择同步执行DBCC，影响是会拉长打开数据库的时间。
 |

    您可以不断单击刷新按钮，来查看数据上云任务最新状态。如果上云失败，请根据任务描述提示排查错误，可参考本文的常见错误部分。


## 导入差异或者日志备份文件 { .section}

SQL Server本地数据库完全备份文件导入上云完成后，接下来需要导入差异备份或者日志备份文件，方法如下：

1.  登录[RDS控制台](https://rdsnew.console.aliyun.com/?spm=a2c63.p38356.a3.17.165742516hVXZz)。
2.  选择目标实例所在地域，单击目标实例的ID，进入基本信息页面。
3.  在左侧菜单栏中选择**备份恢复**。
4.  单击右上角**OSS备份数据恢复上云**。

    在任务列表中找到待导入备份文件的记录，单击记录右侧的**上传增量文件**，打开上传增量文件窗口，如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7996/15439031854345_zh-CN.png)

5.  设置好参数，单击**确认**导入差异或日志备份文件。

    如果您有多个日志备份文件，请使用同样的方法逐个生成上云任务。

    请在上传增量文件时，尽量保证最后一个备份文件的大小不超过500MB，以此来缩短增量上云的时间开销。您可以不断单击**刷新**按钮，来查看数据上云任务最新状态。

    **说明：** 在最后一个日志备份文件生成前，请停止本地环境数据库所有的写入操作，以保证线下数据库和RDS for SQL Server上的数据库数据一致。


## 将数据库上线 { .section}

经过[导入完全备份文件](https://www.alibabacloud.com/#%E5%AF%BC%E5%85%A5%E5%AE%8C%E5%85%A8%E5%A4%87%E4%BB%BD%E6%96%87%E4%BB%B6)、[导入差异或者日志备份文件](https://www.alibabacloud.com/#%E5%AF%BC%E5%85%A5%E5%B7%AE%E5%BC%82%E6%88%96%E8%80%85%E6%97%A5%E5%BF%97%E5%A4%87%E4%BB%BD%E6%96%87%E4%BB%B6)两个步骤后RDS for SQL Server中的数据库会处于In Recovery或者Restoring状态。高可用版本会是In Recovery状态，单机版会是Restoring状态，此时的数据库还无法进行读写操作，需要打开数据库，方法如下：

1.  登录[RDS控制台](https://rdsnew.console.aliyun.com/?spm=a2c63.p38356.a3.20.165742516hVXZz)。
2.  选择目标实例所在地域，单击目标实例的ID，进入基本信息页面。
3.  在左侧菜单栏中选择**备份恢复**。
4.  单击右上角**OSS备份数据恢复上云**。
5.  在任务列表中找到待导入备份文件的记录，单击记录右侧的**打开数据库**，打开打开数据库窗口。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7996/15439031854346_zh-CN.png)

6.  在打开数据库窗口中选择数据库的打开方式。打开数据库一致性检查有以下两种方式：
    -   异步执行DBCC：在打开数据库的时候系统不做DBCC CheckDB，而是在打开数据库任务结束以后，异步执行DBCC CheckDB操作。异步执行DBCC方式节约打开数据库操作的时间（数据库比较大，DBCC CheckDB非常耗时），减少用户的业务停机时间。如果，您对业务停机时间要求非常敏感，且不关心DBCC CheckDB结果，建议使用异步执行DBCC。

    -   同步执行DBCC：相对于异步执行DBCC，有的用户非常关心DBCC CheckDB的结果，以此来找出用户线下数据库数据一致性错误。此时，建议您选择同步执行DBCC。


## 查看备份上云记录 {#section_j5n_bgk_zdb .section}

您也可以查看一段时间内的备份上云记录，具体操作如下：

进入备份恢复页面，选择**备份上云恢复记录**，默认会展示最近一周的记录。当然，您同样可以修改时间范围来查看特定时间段内的上云恢复记录。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7996/15439031854347_zh-CN.png)

## 查看上云任务备份文件详情 {#section_v4r_lcy_b2b .section}

如果您想要查看某个上云任务的所有备份文件详情，方法如下：

进入备份恢复页面，选择备份上云恢复记录，单击对应任务最右侧的**查看文件详情**，弹出查看文件详情页面，展示对应任务所有关联的备份文件详情。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7996/15439031854348_zh-CN.png)

## 常见错误 {#section_oqm_mlw_ydb .section}

全量备份数据上云中常见错误部分请参考[全量备份数据上云SQL Server 2012/2016版本](intl.zh-CN/用户指南/数据迁移/SQL Server备份数据上云/全量备份数据上云SQL Server 2012__2016版本.md#)。用户在增量上云过程中，还有可能会遇到下面的错误。

**数据库打开失败**

错误信息：

```
Failed to open database xxx.
```

**错误原因：**线下SQL Server数据库启用了一些高级功能，备份数据库后通过OSS上云功能迁移到RDS for SQL Server，如果用户选择的RDS SQL版本不支持这些高级功能，会导致数据库打开失败。

比如：本地SQL Server数据库是企业版，启用了数据压缩（Data Compression）或者分区\(Partition\)，OSS上云到RDS for SQL Server Web版，就会报告这个错误。

**以下两种解决方法：**

-   在本地SQL Server实例上禁用高级功能，重新备份后，再使用OSS上云功能。

-   购买与线下SQL Server实例相同版本的RDS for SQL Server，比如，线下是SQL Server 2012企业版，那么请购买RDS for SQL Server 企业单机或者高可用版。


**数据库备份链中LSN无法对接**

**错误信息：**

```
The log in this backup set begins at LSN XXX, which is too recent to apply to the database.
        RESTORE LOG is terminating abnormally.
```

**错误原因：**在SQL Server数据库中，差异备份或者日志备份能够成功还原的前提是，差异或者日志备份的LSN必须与上一次还原的备份文件LSN能够对接上，否则就会报告这个错误。

**解决方法：**请选择对应的LSN备份文件进行增量备份文件上云，一个比较简单的方法是：按照备份文件备份操作时间先后顺利进行增量上云操作。

**异步DBCC CheckDB成功**

**提示信息：**

```
Success to DBCC checkdb asynchronously.
```

**解释：**由于DBCC CheckDB操作比较消耗性能和时间，因此，为了提升用户数据库增量上云的效率，我们采用异步任务来做DBCC CheckDB的方式来检查用户上云数据库的完整性。当你看到这个提示信息时，说明你上云的数据库没有一致性性错误。而与之相反的是下面一个错误“异步DBCC Checkdb失败”。

**异步DBCC Checkdb失败**

**错误信息：**

```
asynchronously DBCC checkdb failed: CHECKDB found 0 allocation errors and 2 consistency
        errors in table 'XXX' (object ID XXX).
```

**错误原因：**用户备份文件还原到RDS for SQL Server上，上云任务系统会异步做DBCC CheckDB检查，如果检查不通过，说明用户数据库在本地环境中已经有错误发生。

**以下两种解决方法：**

-   用户在RDS for SQL Server上执行：

```
DBCC CHECKDB (DBName,REPAIR_ALLOW_DATA_LOSS)
```

    **说明：** 使用该命令修复错误的过程，可能会导致用户数据丢失。

-   删除RDS for SQL Server上对应的数据库，在线下使用：

```
DBCC CHECKDB (DBName,
      REPAIR_ALLOW_DATA_LOSS)
```

修复数据库错误，重新执行数据库增量上云步骤。


**完全备份文件类型**

**错误信息：**

```
Backup set (xxx) is a Database FULL backup, we only accept transaction log or differential backup.
```

**错误原因：**在增量上云RDS for SQL Server过程中，全量备份文件还原完毕后，就只能再接受日志备份文件或者是差异备份文件。如果用户再次选择了全量备份文件，就会报告这个错误。

**解决方法：**请选择日志备份文件或者差异备份文件。

**数据库个数达到最大限制数**

**错误信息：**

```
The database (xxx) migration failed due to databases count limitation.
```

**错误原因：**RDS for SQL Server 2012/2016双机版对用户数据库的个数有50个限制，当用户数据库达到50个以后再做上云操作，任务会失败报告这个错误。RDS for SQL Server 2012/2016单机版中的数据库个数限制是100个，RDS for SQL Server 2008R2不会有这个报错。

**解决方法：**迁移上云数据库到其他的RDS for SQL Server，或者删除不必要的数据库。

**补充说明：**RDS SQL 2012／2016双机版对用户数据库个数限制的原因是当用户数据库过多时，会导致RDS for SQL Server系统本身Mirroring后台占据过多的系统进程（每个用户数据库占用3个系统进程）。当用户数据库过多时，会消耗过多的连接进程，可能会导致用户的连接拿不到Worker资源而连接失败，影响RDS for SQL Server的稳定性。基于用户RDS for SQL Server稳定和高效性为第一优先的原则，我们将RDS for SQL Server 2012/2016高可用版的用户数据库个数限制为50个。

