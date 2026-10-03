---
title: 'Redis（2）：Hash与List'
banner: "/Pho/02.webp"
cover: "/Pho/02.webp"
date: 2026-08-24
categories:
  - 数据库
tags:
  - Redis
  - 命令
  - 记录
toc: true
---

## Set
Redis 中，String 和 Hash 都可以用来存储对象，但它们的底层结构、操作方式和适用场景不同。
**String 是 key-value，适合存整体；Hash 是 key-field-value，适合存对象的多个字段。**，
修改时String只能修改键值的整体，而Hash可以对单个字段进行修改。
{{< gallery >}}
![Hash示意图](/neirong/redis2.webp)
{{< /gallery >}}

## Hash应用场景

| 场景 | 示例 Key | 说明 |
|---|---|---|
| 用户信息 | `user:1001` | name、age、email 等属性 |
| 商品信息 | `product:2001` | name、price、stock |
| 配置项 | `config:app` | 多项配置集中管理 |
| 计数器组 | `stats:page:123` | views、likes、shares |
| Session | `session:abc123` | 登录状态多字段 |
| 购物车 | `cart:user:1001` | 商品 ID → 数量 |
| 限流 | `limit:user:1001` | 多个维度的计数 |


## Redis命令：Hash类型

| 命令 | 作用 | 复杂度 | 常用度 |
|---|---|---|---|
| `HSET` | 设置一个或多个 field 的值 | O(1)/field | ⭐⭐⭐ |
| `HGET` | 获取指定 field 的值 | O(1) | ⭐⭐⭐ |
| `HMGET` | 批量获取多个 field 的值 | O(N) | ⭐⭐⭐ |
| `HGETALL` | 获取所有 field 和 value | O(N) | ⭐⭐⭐ |
| `HDEL` | 删除一个或多个 field | O(N) | ⭐⭐⭐ |
| `HEXISTS` | 判断 field 是否存在 | O(1) | ⭐⭐⭐ |
| `HKEYS` | 获取所有 field 名 | O(N) | ⭐⭐ |
| `HVALS` | 获取所有 field 的值 | O(N) | ⭐⭐ |
| `HLEN` | 获取 field 数量 | O(1) | ⭐⭐ |
| `HINCRBY` | 对整数 field 自增 | O(1) | ⭐⭐⭐ |
| `HINCRBYFLOAT` | 对浮点 field 自增 | O(1) | ⭐⭐ |
| `HSETNX` | 仅当 field 不存在时设置 | O(1) | ⭐⭐ |
| `HSCAN` | 增量迭代 field | O(1)/次 | ⭐⭐ |


## List
Redis List 是一个**有序、可重复**的字符串列表，底层使用 quicklist（或 listpack）实现，
支持从头部或尾部快速插入和弹出元素，支持正向检索和反向检索，查询速度一般。
它既可以当**栈**用，也可以当**队列**用，还常用于消息队列、最新列表等场景。
| 特点 | 说明 |
|:---:|:---:|
| 有序 | 元素按插入顺序排列 |
| 可重复 | 允许存在相同元素 |
| 双端操作 | 支持从头部（Left）和尾部（Right）插入/弹出 |
| 下标访问 | 支持通过索引获取、修改元素 |
| 底层结构 | quicklist（Redis 3.2+），内部由 listpack 组成 |
| 最大长度 | 理论 2^32 - 1 个元素（受内存限制） |
| 阻塞操作 | 支持 BLPOP、BRPOP 等阻塞命令 |
| 原子性 | 单条命令原子执行 |


## List应用场景

| 场景 | 说明 | 常用命令 |
|---|---|---|
| 消息队列 | 生产者 LPUSH，消费者 BRPOP | `LPUSH` + `BRPOP` |
| 栈 | 后进先出 | `LPUSH` + `LPOP` |
| 队列 | 先进先出 | `RPUSH` + `LPOP` |
| 最新列表 | 只保留最近 N 条 | `LPUSH` + `LTRIM` |
| 排行榜 | 简单列表 | `RPUSH` + `LRANGE` |
| 任务队列 | 可靠队列 | `RPOPLPUSH` / `BLMOVE` |
| 历史记录 | 保留最近操作 | `LPUSH` + `LTRIM` |

## Redis命令：List类型

| 命令 | 作用 | 复杂度 | 常用度 |
|---|---|---|---|
| `LPUSH` | 从头部插入一个或多个元素 | O(N) | ⭐⭐⭐ |
| `RPUSH` | 从尾部插入一个或多个元素 | O(N) | ⭐⭐⭐ |
| `LPOP` | 从头部弹出一个或多个元素 | O(N) | ⭐⭐⭐ |
| `RPOP` | 从尾部弹出一个或多个元素 | O(N) | ⭐⭐⭐ |
| `LRANGE` | 获取指定范围的元素 | O(S+N) | ⭐⭐⭐ |
| `LLEN` | 获取列表长度 | O(1) | ⭐⭐⭐ |
| `LINDEX` | 获取指定索引的元素 | O(N) | ⭐⭐ |
| `LSET` | 设置指定索引的元素值 | O(N) | ⭐⭐ |
| `LINSERT` | 在指定元素前/后插入新元素 | O(N) | ⭐⭐ |
| `LREM` | 删除指定数量的匹配元素 | O(N) | ⭐⭐ |
| `LTRIM` | 修剪列表，只保留指定范围 | O(N) | ⭐⭐ |
| `RPOPLPUSH` | 从源列表尾部弹出，插入目标列表头部 | O(1) | ⭐⭐ |
| `LMOVE` | 从源列表一端弹出，插入目标列表一端 | O(1) | ⭐⭐ |
| `BLPOP` | 阻塞式从头部弹出 | O(1) | ⭐⭐⭐ |
| `BRPOP` | 阻塞式从尾部弹出 | O(1) | ⭐⭐⭐ |
| `BRPOPLPUSH` | 阻塞式 RPOPLPUSH | O(1) | ⭐⭐ |
| `BLMOVE` | 阻塞式 LMOVE | O(1) | ⭐⭐ |

> 复杂度说明：N 为列表长度，S 为起始偏移量。



