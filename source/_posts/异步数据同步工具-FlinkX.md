---
title: 异步数据同步工具-FlinkX
date: 2021-07-14 19:27:16
tags:
- FlinkX
- 数据同步
categories: 
- 数据库
cover: false
---

# 介绍

FlinkX是一个基于Flink的批流统一的数据同步工具，既可以采集静态的数据，比如MySQL，HDFS等，也可以采集实时变化的数据，比如MySQL binlog，Kafka等。FlinkX目前包含下面这些特性：

- 大部分插件支持并发读写数据，可以大幅度提高读写速度；
- 部分插件支持失败恢复的功能，可以从失败的位置恢复任务，节约运行时间；[失败恢复](https://github.com/oceanos/flinkx/blob/1.8_release/docs/restore.md)
- 关系数据库的Reader插件支持间隔轮询功能，可以持续不断的采集变化的数据；[间隔轮询](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbreader.md)
- 部分数据库支持开启Kerberos安全认证；[Kerberos](https://github.com/oceanos/flinkx/blob/1.8_release/docs/kerberos.md)
- 可以限制reader的读取速度，降低对业务数据库的影响；
- 可以记录writer插件写数据时产生的脏数据；
- 可以限制脏数据的最大数量；
- 支持多种运行模式；

FlinkX目前支持下面这些数据库：



|                        | Database Type  | Reader                                                       | Writer                                                       |
| ---------------------- | -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Batch Synchronization  | MySQL          | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbwriter.md) |
|                        | Oracle         | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbwriter.md) |
|                        | SqlServer      | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbwriter.md) |
|                        | PostgreSQL     | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbwriter.md) |
|                        | DB2            | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbwriter.md) |
|                        | GBase          | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbwriter.md) |
|                        | ClickHouse     | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbwriter.md) |
|                        | PolarDB        | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbwriter.md) |
|                        | SAP Hana       | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbwriter.md) |
|                        | Teradata       | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbwriter.md) |
|                        | Phoenix        | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbwriter.md) |
|                        | 达梦           | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rdbwriter.md) |
|                        | Cassandra      | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/cassandrareader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/cassandrawriter.md) |
|                        | ODPS           | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/odpsreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/odpswriter.md) |
|                        | HBase          | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/hbasereader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/hbasewriter.md) |
|                        | MongoDB        | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/mongodbreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/mongodbwriter.md) |
|                        | Kudu           | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/kudureader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/kuduwriter.md) |
|                        | ElasticSearch  | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/esreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/eswriter.md) |
|                        | FTP            | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/ftpreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/ftpwriter.md) |
|                        | HDFS           | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/hdfsreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/hdfswriter.md) |
|                        | Carbondata     | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/carbondatareader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/carbondatawriter.md) |
|                        | Redis          |                                                              | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/rediswriter.md) |
|                        | Hive           |                                                              | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/hivewriter.md) |
| Stream Synchronization | Kafka          | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/kafkareader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/kafkawriter.md) |
|                        | EMQX           | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/emqxreader.md) | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/emqxwriter.md) |
|                        | MySQL Binlog   | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/binlog.md) |                                                              |
|                        | MongoDB Oplog  | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/mongodb_oplog.md) |                                                              |
|                        | PostgreSQL WAL | [doc](https://github.com/oceanos/flinkx/blob/1.8_release/docs/pgwalreader.md) |                                                              |

详细见：

​	https://github.com/DTStack/flinkx
