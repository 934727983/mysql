# 什么是RDS {#concept_pc2_lv5_tdb .concept}

阿里云关系型数据库（Relational Database Service，简称RDS）是一种稳定可靠、可弹性伸缩的在线数据库服务。基于阿里云分布式文件系统和SSD盘高性能存储，RDS支持MySQL、SQL Server、PostgreSQL、PPAS（Postgre Plus Advanced Server，高度兼容Oracle数据库）和MariaDB引擎，并且提供了容灾、备份、恢复、监控、迁移等方面的全套解决方案，彻底解决数据库运维的烦恼。

## 学习路径图 {#section_nnr_y4j_b2b .section}

您可以通过[RDS学习路径图](https://help.aliyun.com/learn/learningpath/rds.html)快速了解 RDS的相关概念、基础操作、进阶操作等。

## 优惠活动 {#section_q1p_plv_fgb .section}

试用福利价：RDS for MySQL指定规格首次购买6个月仅需10元。具体请参见[优惠活动](https://m.aliyun.com/markets/aliyun/act/mysql_discount)。

## RDS视频简介 {#section_xd4_y4j_b2b .section}



## 相关概念 {#section_gbt_pd1_42b .section}

了解以下概念，将帮助您更好地选购RDS：

-   实例：实例是虚拟化的数据库服务器。您可以在一个实例中创建和管理多个数据库。
-   地域：地域是指物理的数据中心。一般情况下，RDS实例应该和ECS实例位于同一地域，以实现最高的访问性能。
-   可用区：可用区是指在某个地域内拥有独立电力和网络的物理区域。同一地域的不同可用区之间没有实质性区别。
-   数据库引擎：RDS支持多种数据库引擎：MySQL、SQL Server、PostgreSQL、PPAS（Postgre Plus Advanced Server，高度兼容Oracle数据库）和MariaDB。关于各个引擎的介绍，请参见[数据库引擎](../../../../../cn.zh-CN/用户指南/快速入门.md)。
-   网络类型：您可以选择将实例创建在经典网络或VPC中。专有网络VPC（Virtual Private Cloud）是阿里云上一种隔离的网络环境，安全性比传统的经典网络更高，建议您选择VPC。
-   产品系列：分为基础版、高可用版、集群版和金融版。关于各个系列的介绍，请参见[产品系列概述](cn.zh-CN/产品简介/产品系列/产品系列概述.md)。
-   规格族：分为通用型、独享型、独占物理机型。关于各个规格族的介绍，请参见[实例规格族](cn.zh-CN/产品简介/实例规格/实例规格族.md)。
-   存储类型：分为SSD本地盘与SSD云盘。具体请参见[存储类型](cn.zh-CN/产品简介/存储类型.md)。

## 相关服务 {#section_r45_z21_42b .section}

-   [ECS](../../../../../cn.zh-CN/产品简介/什么是云服务器ECS.md)：ECS是云服务器，通过内网访问同一地域的RDS时，可实现RDS的最佳性能。ECS搭配RDS是典型的业务访问架构。
-   [Redis](../../../../../cn.zh-CN/产品简介/什么是云数据库Redis版.md)：Redis提供持久化的内存数据库服务。当业务访问量较大时，ECS 、RDS和Redis的组合可以支持更多的读请求，同时减少响应时间。
-   [MongoDB](../../../../../cn.zh-CN/产品简介/什么是MongoDB云数据库.md)：提供稳定可靠、弹性伸缩、完全兼容MongoDB协议的数据库服务。数据结构多样时，可以选择将结构化数据存储在RDS，将非结构化数据存储在MongoDB，满足业务的多样化存储需求。
-   [MaxCompute](../../../../../cn.zh-CN/产品简介/什么是MaxCompute.md)：大数据计算服务MaxCompute（原名ODPS）是一种快速、完全托管的TB/PB级数据仓库解决方案，提供了完善的数据导入方案以及多种经典的分布式计算模型，能够快速地解决海量数据计算问题。通过数据集成服务，可将RDS数据导入MaxCompute，实现大规模的数据计算。
-   [DTS](https://help.aliyun.com/document_detail/26592.html)：您可以使用数据传输服务DTS将本地数据库迁移到云上的RDS，以及实现RDS的异地容灾。
-   [OSS](../../../../../cn.zh-CN/产品简介/什么是对象存储 OSS.md)：对象存储服务OSS是阿里云提供的海量、安全、低成本、高可靠的云存储服务。

## 如何使用RDS {#section_rgz_gg1_42b .section}

您可以通过以下方式管理RDS实例，进行实例创建、网络设置、数据库创建、账号创建等操作：

-   [控制台](https://rdsnew.console.aliyun.com)：提供图形化的Web界面，操作方便。
-   CLI：控制台上所有的操作都可以通过CLI实现。
-   [SDK](../../../../../cn.zh-CN/SDK参考/SDK参考.md)：控制台上所有的操作都可以通过SDK实现。
-   [API](../../../../../cn.zh-CN/API参考/API概览.md)：控制台上所有的操作都可以通过API实现。

创建RDS实例后，您可以通过以下方式访问RDS实例：

-   DMS：您可以[通过DMS登录到RDS实例](../../../../../cn.zh-CN/用户指南/附录/通过DMS登录RDS数据库.md)，在Web界面进行数据库开发工作。
-   客户端：RDS兼容原生的数据库协议，您可以使用通用的数据库客户端工具访问RDS实例。例如，MySQL-Front、SSMS（SQL Server Management Studio）、pgAdmin等。

## RDS定价 {#section_kzx_jg1_42b .section}

请参见[收费项目及计费方式](../../../../../cn.zh-CN/产品定价/计费方式与收费项.md)。

