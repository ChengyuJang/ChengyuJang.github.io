---
title: 'Redis（3）：Set与SortedSet'
banner: "/Pho/02.webp"
cover: "/Pho/02.webp"
date: 2026-08-25
categories:
  - 数据库
tags:
  - Redis
  - 命令
  - 记录
toc: true
---

## Set
Redis Set 是一个**无序、不重复**的字符串集合，底层使用 intset 或 hashtable 实现。它支持交集、并集、差集等集合运算，非常适合做标签、去重、共同好友、抽奖等场景。
| 特点 | 说明 |
|:---:|:---:|
| 无序 | 元素没有固定顺序，不能按下标访问 |
| 不重复 | 相同元素只会保留一个 |
| 集合运算 | 支持交集、并集、差集 |
| 随机元素 | 支持随机获取、随机弹出 |
| 底层结构 | 整数集合 intset 或哈希表 hashtable |
| 最大长度 | 理论 2^32 - 1 个元素（受内存限制） |
| 原子性 | 单条命令原子执行 |

## Set应用场景

| 场景 | 说明 | 常用命令 |
|:---:|:---:|:---:|
| 标签系统 | 给文章、用户打标签 | `SADD`、`SMEMBERS` |
| 去重 | 统计独立用户、IP | `SADD`、`SCARD` |
| 共同好友 | 求两个用户共同关注 | `SINTER` |
| 可能认识的人 | 差集推荐 | `SDIFF` |
| 抽奖 | 随机抽取中奖者 | `SRANDMEMBER`、`SPOP` |
| 点赞 | 记录点赞用户，防止重复 | `SADD`、`SISMEMBER` |
| 关注模型 | 关注、粉丝集合 | `SADD`、`SINTER` |
| 黑名单/白名单 | 快速判断是否在集合中 | `SISMEMBER` |

## Redis命令： Set类型

| 命令 | 作用 | 复杂度 | 常用度 |
|---|---|---|---|
| `SADD` | 添加一个或多个元素 | O(N) | ⭐⭐⭐ |
| `SREM` | 删除一个或多个元素 | O(N) | ⭐⭐⭐ |
| `SCARD` | 获取元素数量 | O(1) | ⭐⭐⭐ |
| `SMEMBERS` | 获取所有元素 | O(N) | ⭐⭐⭐ |
| `SISMEMBER` | 判断元素是否存在 | O(1) | ⭐⭐⭐ |
| `SMISMEMBER` | 批量判断多个元素是否存在 | O(N) | ⭐⭐ |
| `SRANDMEMBER` | 随机获取元素（不删除） | O(N) | ⭐⭐ |
| `SPOP` | 随机弹出元素（删除） | O(1) | ⭐⭐⭐ |
| `SMOVE` | 原子移动元素到另一个 Set | O(1) | ⭐⭐ |
| `SINTER` | 交集 | O(N*M) | ⭐⭐⭐ |
| `SINTERSTORE` | 交集并存储 | O(N*M) | ⭐⭐ |
| `SUNION` | 并集 | O(N) | ⭐⭐⭐ |
| `SUNIONSTORE` | 并集并存储 | O(N) | ⭐⭐ |
| `SDIFF` | 差集 | O(N) | ⭐⭐⭐ |
| `SDIFFSTORE` | 差集并存储 | O(N) | ⭐⭐ |
| `SSCAN` | 增量迭代元素 | O(1)/次 | ⭐⭐ |


## SortedSet
Redis Sorted Set（有序集合，简称 ZSet）是一个**元素不重复、每个元素关联一个 score（分数）**的集合。
Redis 会根据 score 自动对元素排序，score 相同则按元素字典序排列。
它非常适合做排行榜、延时队列、带权重的范围查询等。
| 特点 | 说明 |
|:---:|:---:|
| 有序 | 按 score 排序，score 相同按字典序 |
| 元素不重复 | 相同元素只会保留一个，重复添加会更新 score |
| score 可重复 | 多个元素可以有相同 score |
| 范围查询 | 支持按 score、按字典序、按下标范围查询 |
| 排名查询 | 支持获取元素排名（正序/倒序） |
| 底层结构 | listpack 或 skiplist + hashtable |
| 最大长度 | 理论 2^32 - 1 个元素（受内存限制） |
| 原子性 | 单条命令原子执行 |
| 适用场景 | 排行榜、延时队列、范围查询、权重排序 |

## SortedSet应用场景

| 场景 | 说明 | 常用命令 |
|:---:|:---:|:---:|
| 排行榜 | 按分数排名 | `ZADD`、`ZREVRANGE`、`ZRANK` |
| 延时队列 | score 存执行时间戳 | `ZADD`、`ZRANGEBYSCORE`、`ZREM` |
| 权重排序 | 按权重排序元素 | `ZADD`、`ZRANGE` |
| 范围查询 | 按 score 范围取数据 | `ZRANGEBYSCORE` |
| 热搜榜 | 按热度排序 | `ZINCRBY`、`ZREVRANGE` |
| 附近的人 | 配合 GEO 使用 | `GEOADD`、`GEORADIUS` |
| 抽奖 | 随机抽取 | `ZRANDMEMBER`、`ZPOPMIN` |


## Redis命令：SortedSet类型

| 命令 | 作用 | 复杂度 | 常用度 |
|---|---|---|---|
| `ZADD` | 添加元素并设置 score | O(log N) | ⭐⭐⭐ |
| `ZREM` | 删除一个或多个元素 | O(M log N) | ⭐⭐⭐ |
| `ZSCORE` | 获取元素的 score | O(1) | ⭐⭐⭐ |
| `ZMSCORE` | 批量获取多个元素的 score | O(N) | ⭐⭐ |
| `ZINCRBY` | 增加元素的 score | O(log N) | ⭐⭐⭐ |
| `ZCARD` | 获取元素数量 | O(1) | ⭐⭐⭐ |
| `ZCOUNT` | 统计 score 范围内的元素数量 | O(log N) | ⭐⭐⭐ |
| `ZRANK` | 获取元素正序排名 | O(log N) | ⭐⭐⭐ |
| `ZREVRANK` | 获取元素倒序排名 | O(log N) | ⭐⭐⭐ |
| `ZRANGE` | 按范围获取元素 | O(log N + M) | ⭐⭐⭐ |
| `ZREVRANGE` | 按范围倒序获取元素 | O(log N + M) | ⭐⭐⭐ |
| `ZRANGEBYSCORE` | 按 score 范围获取元素 | O(log N + M) | ⭐⭐⭐ |
| `ZREVRANGEBYSCORE` | 按 score 范围倒序获取 | O(log N + M) | ⭐⭐⭐ |
| `ZRANGEBYLEX` | 按字典序范围获取元素 | O(log N + M) | ⭐⭐ |
| `ZREVRANGEBYLEX` | 按字典序倒序获取 | O(log N + M) | ⭐⭐ |
| `ZREMRANGEBYRANK` | 按下标范围删除元素 | O(log N + M) | ⭐⭐ |
| `ZREMRANGEBYSCORE` | 按 score 范围删除元素 | O(log N + M) | ⭐⭐ |
| `ZPOPMIN` | 弹出 score 最小的元素 | O(log N) | ⭐⭐⭐ |
| `ZPOPMAX` | 弹出 score 最大的元素 | O(log N) | ⭐⭐⭐ |
| `BZPOPMIN` | 阻塞式弹出 score 最小元素 | O(log N) | ⭐⭐ |
| `BZPOPMAX` | 阻塞式弹出 score 最大元素 | O(log N) | ⭐⭐ |
| `ZUNIONSTORE` | 并集并存储 | O(N log N) | ⭐⭐ |
| `ZINTERSTORE` | 交集并存储 | O(N log N) | ⭐⭐ |
| `ZDIFFSTORE` | 差集并存储 | O(N log N) | ⭐⭐ |
| `ZUNION` | 并集（Redis 6.2+） | O(N log N) | ⭐⭐ |
| `ZINTER` | 交集（Redis 6.2+） | O(N log N) | ⭐⭐ |
| `ZDIFF` | 差集（Redis 6.2+） | O(N log N) | ⭐⭐ |
| `ZSCAN` | 增量迭代元素 | O(1)/次 | ⭐⭐ |
>注意：所有的排名默认都是升序，如果要降序则在命令的Z后面添加REV，ZRANK->ZREVRANK


