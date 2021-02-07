# MongoDB Indexes

## 基本操作

```
db.collection.createIndex(
	{field1: 1, field2: -1, ...}, # keys
	{'background': true, 'unique': true, 'name': 'indexName', 'sparse': false, ...},	# options
	commitQuorum
)
```

```
db.collection.dropIndex(indexName)
```

```
db.collection.getIndexes()
```

## 用途

- speed query
- speed sort

## 规则

最前缀匹配`prefix match`:

索引`{'a': 1, 'b': 1}` 匹配 `{'a':1}` , `{'a': 1, 'b': 1}`查询条件

所以，一般最常用的查询条件放在最前面

# 设计索引

- 只加速query

  Idx: `{'a': 1, 'b': 1}`

  `db.collection.find({'a': {''$gt': 1}, 'b': 2}).sort({'c': 1})`

  查询阶段利用Index 过滤，减少从磁盘`fetch data`，但排序仍需在内存中进行

- 只加速sort

  Idx: `{'a': 1, 'b':1}`
  
  `db.collection.find({'c': 1}).sort({'a': 1, 'b': 1})`
  
  查询阶段一边遍历Index tree一边应用`{'c': 1}` 过滤，`fetch data` 是全集`idx scan`

这两种都不是最好的索引设计，应该尽量让`query`和`sort`阶段都使用索引

- 最简单 query, sort 都是idx的prefix match

  Idx: `{'a': 1, 'b': 1, 'c': 1}`

  `db.collection.find({'a': 1}).sort({'a': 1, 'b': 1})`

  `db.collection.find({'a': 1, 'b': 1}).sort({'a': 1, 'b': 1, 'c': 1})`


- Non-prefix match:

  Idx: `{'a': 1, 'b': 1, 'c': 1}`

  ```
  db.collection.find({'a': 100, 'b': 100}).sort({'c': 1})
  ```

  要求 idx `'c'` field前面的field在query中为equal condition`
  
  ~~`db.collection.find({'a': 100}).sort({'c': 1})`~~
  
  ~~`db.collection.find({'a': 100, 'b': {'gt': 10}}).sort({'c': 1})`~~

# 常见

```
db.clan.find()
db.clan.count()
db.clan.find().explain()
db.clan.find().explain('executionStats')
```

