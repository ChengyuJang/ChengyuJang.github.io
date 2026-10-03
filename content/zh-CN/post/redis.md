---
title: 'Redis（1）'
banner: "/Pho/02.webp"
cover: "/Pho/02.webp"
date: 2026-08-23
categories:
  - 数据库
tags:
  - Redis
  - 学习
  - 记录
toc: true
---

## Redis与SQL的区别

| 对比维度 | Redis | SQL |
| :---: | :---: | :---: |
| 类型 | NoSQL、键值型、内存数据库 | 关系型、SQL 数据库 |
| 数据模型 | key-value；String、Hash、List、Set、ZSet、Stream | 表、行、列、外键、关系 |
| Schema | 无固定 Schema，灵活 | 需预定义表结构，约束严格 |
| 查询语言 | Redis 命令 / API | SQL查询有通用格式 |
| 关系查询 | 不支持 JOIN | 支持 JOIN、子查询、聚合 |
| 事务 | MULTI / EXEC，有限，通常不支持回滚 | BEGIN / COMMIT / ROLLBACK，ACID |
| 并发 | 单线程命令原子，Lua 原子 | MVCC、锁、隔离级别 |
| 持久化 | RDB / AOF，可能丢数据 | WAL / redo / undo，可靠性高 |
| 存储 | 内存为主 | 磁盘为主，内存缓存 |
| 性能 | 低延迟、高吞吐，适合简单 KV | 复杂查询强，简单高频读写通常不如 Redis |
| 扩展 | 主从、哨兵、Cluster 分片 | 主从、读写分离、分库分表 |
| 容量 | 受内存限制 | 受磁盘限制，通常更大 |
| 索引 | 数据结构 / 模块，二级索引需设计 | B+树、哈希、全文等 |
| 典型场景 | 缓存、会话、排行榜、计数器、队列 | 交易、订单、用户、报表、复杂分析 |
| 典型产品 | Redis、Valkey、KeyDB、Dragonfly | MySQL、PostgreSQL、Oracle、SQL Server、SQLite |

>Redis 是内存型 NoSQL 键值数据库，强调速度、简单结构和灵活扩展；
SQL 数据库是关系型数据库，强调表结构、复杂查询、事务和强一致。
二者混搭，Redis 做缓存和高速读写，SQL 数据库做持久化和业务数据管理。


## Redis数据结构

| 数据结构 | 英文名 | 特点 | 常用场景 |
|---|---|---|---|
| 字符串 | String | 二进制安全，可存文本、数字、二进制 | 缓存、计数器、分布式锁 |
| 列表 | List | 有序、可重复，支持双端操作 | 消息队列、最新列表、栈/队列 |
| 集合 | Set | 无序、不重复 | 标签、去重、共同好友 |
| 哈希 | Hash | 键值对集合，适合存对象 | 用户信息、商品信息 |
| 有序集合 | Sorted Set / ZSet | 每个元素带 score，按 score 排序 | 排行榜、延时队列 |
| 位图 | Bitmap | 基于 String 的位操作 | 签到、活跃用户统计 |
| 基数统计 | HyperLogLog | 近似去重计数，有误差 | UV 统计、独立访客 |
| 地理空间 | Geospatial | 存储经纬度，支持距离计算 | 附近的人、门店距离 |
| 流 | Stream | 消息队列，支持消费者组 | 事件流、消息队列 |


## Redis启动

```bash
#启动服务
redis-server
#打招呼返回PONG
redis-cli ping
#进入
redis-cli
```

## Redis常用命令查询方式

```bash
#help 命令 查询命令的使用方式
help keys 
```


## Redis通用命令

Redis 通用命令（Generic Commands）是一组**不依赖具体数据类型**的命令，可以作用于任何类型的 key，
比如 String、List、Hash、Set、ZSet 等。它们主要用于 key 的管理、过期时间控制、数据库切换和服务器状态查询等。
| 命令 | 作用 | 常用度 |
|---|---|---|
| `KEYS` | 查找符合模式的 key | ⭐⭐ |
| `SCAN` | 增量迭代 key（推荐替代 KEYS） | ⭐⭐⭐ |
| `EXISTS` | 判断 key 是否存在 | ⭐⭐⭐ |
| `DEL` | 删除一个或多个 key | ⭐⭐⭐ |
| `UNLINK` | 异步删除 key（非阻塞） | ⭐⭐ |
| `EXPIRE` | 设置 key 的过期时间（秒） | ⭐⭐⭐ |
| `TTL` | 查看 key 剩余存活时间（秒） | ⭐⭐⭐ |
| `SELECT` | 切换数据库 | ⭐⭐ |
| `DBSIZE` | 返回当前数据库的 key 数量 | ⭐⭐ |
| `PING` | 测试连接是否正常 | ⭐⭐⭐ |
| `INFO` | 查看服务器信息 | ⭐⭐ |
>`KEYS *` 会遍历所有 key，在 key 数量多时会**阻塞 Redis**。生产环境严禁使用，应改用 `SCAN`。  
`TTL`返回(integer) -2时值已经不存在,回(integer) -1值永久存在


## Redis命令：String类型
Redis String 是最基础、最常用的数据类型，有字符串、int、float三种类型。
String 类型是二进制安全的，最大长度为 **512MB*，

| 命令 | 作用 | 常用度 |
|---|---|---|
| `SET` | 设置 key 的值 | ⭐⭐⭐ |
| `GET` | 获取 key 的值 | ⭐⭐⭐ |
| `MSET` | 批量设置多个 key | ⭐⭐⭐ |
| `MGET` | 批量获取多个 key | ⭐⭐⭐ |
| `SETNX` | 仅当 key 不存在时设置 | ⭐⭐⭐ |
| `SETEX` | 设置值并指定过期时间（秒） | ⭐⭐⭐ |
| `GETDEL` | 获取值并删除 key | ⭐⭐ |
| `GETEX` | 获取值并设置/移除过期时间 | ⭐⭐ |
| `APPEND` | 追加内容 | ⭐⭐ |
| `STRLEN` | 获取字符串长度 | ⭐⭐ |
| `INCR` | 原子自增 1 | ⭐⭐⭐ |
| `DECR` | 原子自减 1 | ⭐⭐⭐ |
| `INCRBY` | 原子增加指定整数 | ⭐⭐⭐ |
| `DECRBY` | 原子减少指定整数 | ⭐⭐⭐ |
| `INCRBYFLOAT` | 原子增加指定浮点数 | ⭐⭐ |
| `GETRANGE` | 获取子字符串 | ⭐⭐ |
| `SETBIT` | 设置二进制位 | ⭐⭐ |
| `GETBIT` | 获取二进制位 | ⭐⭐ |
| `BITCOUNT` | 统计为 1 的二进制位数量 | ⭐⭐ |


## Redis Key的层级格式
Redis 本身没有真正的目录、文件夹或表结构。它的 key 本质上就是一个**二进制安全的字符串**，是扁平的。
但是，为了方便管理和阅读，社区约定使用**冒号 `:`** 来分隔 key 的层级，模拟出类似目录树的结构。  
>项目名:业务名:类型:id

比如有一个项目名称为caigou，有store和product两种不同的类型数据，定义key为
>store相关的key: caigou:store:1   
product相关的key: caigou:product:1

```bash
set caigou:store:1 '{"id":1,"name":HUAWEI,"time":2025/9/2}'
set caigou:product:1 '{"id":1,"name":phone,"number":20}'
```
