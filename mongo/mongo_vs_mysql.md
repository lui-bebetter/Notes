MySQL vs Mongo:
1.类似于c/c++ vs Python, Python的数组里面可以存储任何类型，很方便，但会占用更多内存，
当需要对此进行优化是，依然会通过c++来进行属性特例化。MySQL适用于数据结构比较固定的应用场景

2. Mongo的复制和分片可以支持海量数据
3. MySQL事务支持比Mongo好，mongo文档级别原子性,更高的一致性和可靠性
4. MySQL数据结构固定，结构化查询语句支持更复杂的查询

MYSQL vs. postgreSQL vs. oracle
1.都是关系型数据库. 开源：MYSQL, postgreSQL 商用：oracle
2.postgreSQL最接近oracle性能的开源数据库
3.只考虑性能：MySQL << postgreSQL ~= Oracle