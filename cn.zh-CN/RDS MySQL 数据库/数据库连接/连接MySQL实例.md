# 连接MySQL实例

初始化配置后，您可以让ECS连接MySQL实例，也可以本地连接到MySQL实例，实现业务目标。

完成创建实例、设置白名单、创建账号等操作后，您可以使用数据管理服务DMS（Data Management Service）或通用数据库客户端连接到MySQL实例，也可以在应用程序中配置地址、端口、账号信息等进行连接。

其他引擎连接实例请参见：

-   [连接SQL Server实例](/cn.zh-CN/RDS SQL Server 数据库/快速入门/连接SQL Server实例.md)
-   [连接PostgreSQL实例](/cn.zh-CN/RDS PostgreSQL 数据库/快速入门/连接PostgreSQL实例.md)
-   [连接PPAS实例](/cn.zh-CN/RDS PPAS 数据库/快速入门/连接PPAS实例.md)
-   [连接MariaDB实例](/cn.zh-CN/RDS MariaDB TX 数据库/快速入门/连接MariaDB实例.md)

## 方法一：使用DMS连接实例

DMS是阿里云提供的图形化的数据管理工具，可用于管理关系型数据库和NoSQL数据库，支持数据管理、结构管理、用户授权、安全审计、数据趋势、数据追踪、BI图表、性能与优化等功能。

您可以在**数据库管理**页面右侧单击**SQL查询**登录数据库。

![SQL查询](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8414713061/p174701.png)

具体操作请参见[通过DMS登录RDS数据库](/cn.zh-CN/RDS MySQL 数据库/数据库连接/通过DMS登录RDS数据库.md)。

## 方法二：使用客户端连接实例

由于RDS与原生的数据库服务完全兼容，所以您可以使用任何通用的数据库客户端连接到RDS实例，且连接方法类似。下文以[HeidiSQL](https://www.heidisql.com/)为例。

1.  启动HeidiSQL客户端。
2.  在左下角单击**新建**。
3.  输入要连接的RDS实例信息，参数说明如下。

    ![连接信息](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8613729951/p54911.png)

    |参数|说明|
    |--|--|
    |**网络类型**|连接数据库的形式。选择**MariaDB or MySQL（TCP/IP）**。|
    |**主机名/IP地址**|输入RDS实例的内网地址或外网地址。     -   若您的客户端部署在ECS实例上，且ECS实例与要访问的RDS实例的地域、网络类型相同，请使用内网地址。例如ECS实例和RDS实例都是华东1的专有网络实例，使用内网地址连接能提供安全高效的访问。
    -   其它情况只能使用外网地址。
查看RDS实例的内外网地址及端口信息请参见[查看或修改内外网地址和端口](/cn.zh-CN/RDS MySQL 数据库/数据库连接/查看或修改内外网地址和端口.md)。 |
    |**用户**|RDS实例中的数据库账号名称。|
    |**密码**|账号对应的密码。|
    |**端口**|若使用内网连接，需输入RDS实例的内网端口。若使用外网连接，需输入RDS实例的外网端口。详情请参见[查看或修改内外网地址和端口](/cn.zh-CN/RDS MySQL 数据库/数据库连接/查看或修改内外网地址和端口.md)。|

4.  单击**打开**。

    若连接信息无误，即会成功连接实例。

    ![连接成功](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9613729951/p2610.png)


## 方法三：使用命令行连接实例

如果您的服务器安装了MySQL，可以通过命令行连接RDS MySQL数据库，连接方式如下：

```
mysql -h<连接地址> -P<端口> -u<用户名> -p -D<数据库>
```

```
mysql -hrm-bp1457xxxxxx.mysql.rds.aliyuncs.com -P3306 -utestuser -p
```

|选项|说明|示例|
|--|--|--|
|-h|RDS实例的内网连接地址或外网连接地址。 查看RDS实例的内外网连接地址及端口信息请参见[查看或修改内外网地址和端口](/cn.zh-CN/RDS MySQL 数据库/数据库连接/查看或修改内外网地址和端口.md)。

|`rm-bpxxxxxxxxxxxxxx.mysql.rds.aliyuncs.com`|
|-P|RDS实例的端口号。 -   若使用内网连接，需输入RDS实例的内网端口。
-   若使用外网连接，需输入RDS实例的外网端口。

**说明：**

-   注意P需要大写。
-   默认端口为3306。
-   如果端口号为默认端口，该参数可以不填。

|`3306`|
|-u|RDS实例中的数据库账号名称。|`testuser`|
|-p|以上账号的密码。 **说明：**

-   为保障密码安全，`-p`后请不要填写密码，会在执行整行命令后提示您输入密码，输入后按回车即可登录。
-   如果填写该参数，`-p`与数据库密码之间不能有空格。

|`password123`|
|-D|需要登录的数据库名称。 **说明：**

-   该参数非必填参数。
-   可以不输入`-D`仅输入数据库名称。

|`mysql`|

![命令行连接示例](../images/p52311.png "命令行连接示例")

## 连接失败的解决办法

请参见[解决无法连接实例问题](/cn.zh-CN/常见问题/连接/网络/解决无法连接RDS实例的问题.md)。

## 操作视频

[ECS（Linux）连接RDS](/cn.zh-CN/视频专区/ECS（Linux）连接RDS.md)

## 常见问题

Q：我使用函数计算，想获取RDS的数据，要怎么操作呢？

A：您可以为函数安装第三方依赖，使用内置模块获取RDS数据，详情请参见[为函数安装第三方依赖](https://help.aliyun.com/document_detail/74571.html)。

