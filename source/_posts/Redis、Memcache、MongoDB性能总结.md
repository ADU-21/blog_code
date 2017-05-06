---
title: Redis、Memcache、MongoDB性能总结
date: 2016-04-03 21:03:55
categories:
- 学习总结
tags: DataBase
---
# Redis、Memcache、MongoDB有哪些区别？
<!-- more -->
## Memcached
### Memcached的优点：
Memcached可以利用多核优势，单实例吞吐量极高，可以达到几十万QPS（取决于key、value的字节大小以及服务器硬件性能，日常环境中QPS高峰大约在4-6w左右）。适用于最大程度扛量。

支持直接配置为session handle。
坑少。
### Memcached的局限性：
只支持简单的key/value数据结构，不像Redis可以支持丰富的数据类型。
无法进行持久化，数据不能备份，只能用于缓存使用，且重启后数据全部丢失。
无法进行数据同步，不能将MC中的数据迁移到其他MC实例中。
Memcached内存分配采用Slab Allocation机制管理内存，value大小分布差异较大时会造成内存利用率降低，并引发低利用率时依然出现踢出等问题。需要用户注重value设计。

## Redis
### Redis的优点：
支持多种数据结构，如 string（字符串）、 list(双向链表)、dict(hash表)、set(集合）、zset(排序set)、hyperloglog（基数估算）
支持持久化操作，可以进行aof及rdb数据持久化到磁盘，从而进行数据备份或数据恢复等操作，较好的防止数据丢失的手段。
支持通过Replication进行数据复制，通过master-slave机制，可以实时进行数据的同步复制，支持多级复制和增量复制，master-slave机制是Redis进行HA的重要手段。
单线程请求，所有命令串行执行，并发情况下不需要考虑数据一致性问题。
支持pub/sub消息订阅机制，可以用来进行消息订阅与通知。
支持简单的事务需求，但业界使用场景很少，并不成熟。

### Redis的局限性：
Redis只能使用单线程，性能受限于CPU性能，故单实例CPU最高才可能达到5-6wQPS每秒（取决于数据结构，数据大小以及服务器硬件性能，日常环境中QPS高峰大约在1-2w左右）。
支持简单的事务需求，但业界使用场景很少，并不成熟，既是优点也是缺点。
Redis在string类型上会消耗较多内存，可以使用dict（hash表）压缩存储以降低内存耗用。

：）以下是我个人的补充
Mc和Redis都是Key-Value类型，不适合在不同数据集之间建立关系，也不适合进行查询搜索。比如redis的keys pattern这种匹配操作，对redis的性能是灾难。

## Mogodb
mogodb是一种文档性的数据库。先解释一下文档的数据库，即可以存放xml、json、bson类型系那个的数据。这些数据具备自述性（self-describing），呈现分层的树状数据结构。redis可以用hash存放简单关系型数据。
mogodb存放json格式数据。
适合场景：事件记录、内容管理或者博客平台，比如评论系统。

[原文链接](https://segmentfault.com/q/1010000002588088/a-1020000002589415)

