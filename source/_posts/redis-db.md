---
title: Redis 数据结构
date: 2020-02-05 17:21:35
tags: Redis
category: lengyubingcheng
---

## Redis 数据结构简介

Redis 可以存储键（key）与 5 种不同类型的值（value）之间的映射，这5中不同数据结构类型分别为：STRING、LIST、SET、HASH、ZSET。

以下介绍均采用 redis-cli 交互式客户端。

<!--more-->

1. ### STRING（字符串）

   可以存储字符串、整数或者浮点数。

   #### 增：

   ​	`set key-name value`

   #### 删：

   ​	`del key-name`                  -- 若不存在，则返回 0。

   #### 查：

   ​	`get key-name`                  -- 若不存在，则返回 nil。

   #### 改：

   ##### 自增自减：

   `incr key-name `              -- 将键存储的值加上 1             

   `decr key-name`                 -- 将键存储的值减去 1

   `incrby key-name amount`        -- 将键存储的值加上整数 amount

   `decrby key-name amount`        -- 将键存储的值减去整数 amount

   `incrbyfloat key-name amount`   -- 将键存储的值加上浮点数 amount
   注：当用户存储值的时候，如果可以被解释为十进制整数或者浮点数，则 Redis 会允许用户对其进行上述操作。返回修改后的数值。

   ##### 处理字串和二进制：

   `append key-name value`         -- 追加在值的末尾

   `getrange key-name start end`   -- 获取子字符串，闭区间

   `setrange key-name offset value` -- 将从偏移量 offset 开始的子串设置为 value

   `getbit key-name offset`         -- 将字符看作二进制串，获取串中偏移量 offset 的二进制值

   `setbit key-name offset value`   -- 将串中偏移量offset的二进制值设置为value

   `bitcount key-name [start end]`  --统计二进制串中1的数量，默认为整串，子串可选

   `bitop opration dest-key key-name [keyname ...]` --对多个串进行按位运算操作，保存在 dest-key 中

   ------

   

2. ### LIST（列表）

   #### 增：

   `lpush/rpush key-name value`   -- 将给定值推入列表的左/右端

   #### 删：

   `lpop/rpop key-name`           -- 将列表的左/右端弹出一个值，并返回被弹出的值

   #### 查：

   `lrange key-name start end`    -- 获取列表在给定范围内的所有值：
   若结束为 -1，则取到结尾。

   `lindex key-name index`        -- 获取列表给定位置上的值：

   #### 改：

   `ltrim key-name start end`     -- 修剪，保留[start,end]区间的元素，闭区间

   ##### 阻塞式弹出元素：

   `blpop key-name [key-name..] timeout`    -- 从第一个非空列表中弹出最左端元素，或者在 timeout 秒内阻塞并等待可弹出的元素出现

   `brpop key-name [key-name..] timeout`    -- 从第一个非空列表中弹出最右端元素，或者在 timeout 秒内阻塞并等待可弹出的元素出现

   ##### 列表间移动元素：

   `rpoplpush source-key dest-key`          -- 从 source-key 列表中弹出位于最右边的元素，推到 dest-key 的最左端，并返回这个元素

   `brpoplpush source-key dest-key timeout` -- 从 source-key 列表中弹出位于最右边的元素，推到 dest-key 的最左端，并返回这个元素；若前者为空，则阻塞 timeout 秒等待可弹出元素的出现。

   ------

   

3. ### SET（集合）

   列表可以存储相同的值，集合通过散列表保证存储的值各不相同。

   #### 增：

   `sadd key-name item [item...]`    --   返回正确添加的数量

   #### 删：

   `srem key-name item [item...]`    -- 返回删除的数量
   
   `spop key-name`               -- 随机删除一个元素，并返回该元素
			   
   #### 查：

   `smembers key-name`             -- 返回所有元素

   `sismember key-name`            -- 检查元素是否在集合中,返回 1 存在；0 不存在。

   `srandmember key-name [count]`      --返回集合中 count 的**绝对值**个元素，若 count为**正数**，**不会重复**，即使超过集合长度，只返回**整个集合**，不会重复；若为**负数**，无论 count 多大，都**可能出现重复**。

   `scard key-name`                 -- 返回集合元素的数量 

   #### 改：

   `smove source-key dest-key item`  --从原集合中删除 item 元素，并将 item 添加到目标集合

   #### 其他：

   ##### 交集运算：

   `sinter key-name [key-name]` --返回存在于所有集合中的元素

   `sdinterstore dest-key key-name [key-name]`--同上，并且存储在 dest-key 中

   ##### 并集运算：

   `sunion key-name [key-name]` --返回存在于至少一个集合中的元素 

   `sunionstore dest-key key-name [key-name]`--同上，并且存储在 dest-key 中

   ##### 差集运算:

   `sdiff key-name [key-name]` --返回存在于第一个集合，但不存在其他集合中的元素

   `sdiffstore dest-key key-name [key-name]`--同上，并且存储在 dest-key 中

   

------



4. ### HASH（散列）

   在很多方面像缩小版的 redis，某些方面可以看成**关系型数据库**的**行**，或者**文档数据库**中的**文档**。

   #### 增：

      `hset key-name key value`              -- 返回 1 成功；0 失败（已存在）
   
      `hmset key-name key value [key value]`    --为散列添加多个键值对
   
   #### 删：
   
     `hdel key-name keyh`                 -- 返回 1 成功；0 失败（不存在）
   
     `hmdel key-name key value [key value]`   ---删除多个键值对，并返回正确删除的个数
   
   #### 查：
   
     `hgetall key-name`                  -- 返回所有元素
   
     `hget key-name key `                -- 返回指定散列键的值
   
     `hmget key-name key [key] `  	-- 返回多个键的值
   
     `hlen key-name`         --返回键值对数量
   
     `hexist key-name key`     -- 检查散列中是否存在该键
   
     `hkeys key-name`    --返回所有键
   
     `hvals key-name`    --返回所有值
   
     `hgetall key-name`   --返回所有键值对
   
     `hincrby key-name key increment` --将 key 的值加上整数 increment
   
     `hincrbyfloat key-name key increment`   --  将 key 的值加上浮点数 increment

------



5. ### ZSET（有序集合）

      储存键值对；
      **键** 被称为 **成员**（member），每个成员各不相同；
      **值** 被称为 **分值** （score），必须为 **浮点数**。
      可以根据成员访问元素，也可以根据分值以及分值的排列顺序来访问元素的结构。
      
      #### 增：
      
     `zadd key-name score member`             -- 注意，分值在前；返回 1 成功；0 失败（已存在）
     
      #### 删：
     
     `hrem key-name member`                 -- 返回 1 成功；0 失败（不存在）
     
     `zremrangebyrank key-name min max`   --删除介于此排名之间的所有成员
     
     `zremrangebyscore key-name min max`   --删除介于此分值之间的所有成员
     
     #### 查：
     
      `zcard key-name`  --返回有序集合中包含的成员数量
     
      `zrange key-name start end [withscores]`      -- 根据元素在有序排列中所处的位置（从小到大），获取多个元素，可以选择是否带分值一起返回
     
      `zrevrange key-name start end [withscores]`      -- 根据元素在有序排列中所处的位置（从大到小），获取多个元素，可以选择是否带分值一起返回
     
      `zrangebyscores key-name min max [withscores]` -- 获取有序集合在给定分支范围内的所有元素，从小到大排
     
      `zrevrangebyscores key-name max min [withscores]` -- 获取有序集合在给定分支范围内的所有元素,从大到小排
     
      `zcount key-name min max`   --返回[ min， max]之间的成员数量
     
      `zrank key-name member` --返回成员 member 的排名，按分值从小到大排名
     
      `zrevrank key-name member` --返回成员 member 的排名，按分值从大到小排名
     
      `zscore key-name member `--返回成员的分值
     
      #### 改：
     
     `zincrby key increment member` --将成员的分值加上 increment
     
      #### 其他：
     
      ##### 并集运算
     
     `zunionstore dest-key key-count key [key...] [weight [weight..]] [sum|min|max]`-- 默认为sum，合并的时候将分值相加，结果存在 dest-key 中 ，key-count 指定 key 的数量。
     
      #####    交集运算：

     `zinterstore dest-key key-count key [key...] [weight [weight..]] [sum|min|max]`-- 默认为sum，合并的时候将分值相加，结果存在 dest-key 中 ，key-count 指定 key 的数量。

