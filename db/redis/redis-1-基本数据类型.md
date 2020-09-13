redis：开源 键值对存储系统 单线程 纯内存 

`单线程`:一次执行一个命令

##### 通用命令:

```bash
keys [patter] #遍历所有key O(n)
dbsize #key的总数 O(1)
exists key #判断key是否存在 O(1)
del key [key ...] #删除指定的key O(1)
expire key seconds #给key设置过期时间 O(1)
ttl key #key还有多长时间过期 O(1)
persist key #key永不过期 O(1)
type key #返回key的类型 O(1)
```

##### 字符串键值结构

key :字符串

value:五种数据类型

```bash
set key value
get key
del key
mset key1 vaue1 [key2 value2 ...]
mget key1 key2 ...
getset key newValue #设置新值，返回旧值
append key appendValue #给value拼接值
strlen key #返回value的长度 utf-8 一个中文两个字节
```

##### hashmap

特点:`Mapmap`

`key`  `field  value`

​         ` field  value`

​          `field  value`

```bash
# H开头
hset key field1 value1 [field2 value2]  #hmset
hget key field1  #hmget
hdel key field1
hexists key field #判断hash key中是否有field
hlen key #获取hash key field的数量
hgetall key #返回hash key对应所有的field和value
hvals key #返回hash key对应所有field的值
hkeys key #返回hash key对应所有field

```

##### list

特点:`有序` `可以重复 ` `左右两边插入弹出`

`key`   `ele--ele--ele`

```bash
#添加
rpush key value1 value2 ... valueN #右侧插入
lpush key value1 value2 ... valueN #左侧插入
linsert key before|after value newValue #在指定的value前后插入值
#删除
lpop key #删除最左侧的value
rpop key #删除最右侧的value
lrem key count value 
# count > 0 从左往右，删除最多count个value相等的项
# count < 0 从右往左，删除最多Math.abs(count)个value相等的项
# count = 0 删除所有value的项
ltrim key start end #按索引修剪列表
#查找
lrange key start end  #按照索引获取
index key index #指定索引获取
llen key #获取列表的长度
#修改
lset key index newValue #将index保存中value设置为newValue
```

##### set

特点: `无序`  `无重复` `集合间操作`

`key`  `ele` `ele`

​    `ele`  `ele`    `ele` 

`ele`

```bash
#S开头
sadd key ele #向集合key中添加ele
srem key ele #删除集合key中ele

scard key#计算集合大小
sismember key ele #判断ele是否在集合Key中
srandmember key count #从集合key中随机挑选count个元素
spop key  #从集合Key中随机弹出(删除)一个元素
smembers key #返回集合所有元素

#集合操作
sdiff key1 key2 #差集
sinter key1 key2 #交集
sunion key1 key2 #并集
sdiff|sinter|sunion store destkey #将差集 交集 并集结果保存在destkey中
```

##### zset

有序集合

`key` `score` `value`

​       `score ` `value`

​       `score` `value`

```bash
#以z开头
zadd key score ele [score ele ...] #增加score ele
zrem key score ele [score ele ...] #删除score ele
zscore key ele #返回ele的score
zincrby key increScore ele #ele的score增加increScore
zcard key #返回集合中元素的个数
zrange key start end [withscores] #返回索引范围内的升序元素[分值]
zrangebyscore key minScore maxScore [withscores]#返回执行分数范围内的升序元素
zcount key minScore maxScore  #返回集合内在指定分数范围内的个数
zremrangebyrank key start end #删除指定排名内的升序元素
zremrangebyscore key minScore maxScore #删除指定分数内的升序元素
```

