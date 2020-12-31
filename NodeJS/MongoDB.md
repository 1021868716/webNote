# mongoDB

关系型数据库通过表格的形式存储数据，可以通过列来对不同的表格建立联系。

mongoDB是非关系型数据库，非关系型数据库不是表存储行，可以存储多种数据结构，例如redis-键值对存储，mongoDB-文档存储，还有其他非关系数据库存储对象，图等。

mongoDB是使用集合（ collection）来存储文档（document）

| SQL术语/概念 | MongoDB术语/概念 | 解释/说明                           |
| :----------- | :--------------- | :---------------------------------- |
| database     | database         | 数据库                              |
| table        | collection       | 数据库表/集合                       |
| row          | document         | 数据记录行/文档                     |
| column       | field            | 数据字段/域                         |
| index        | index            | 索引                                |
| table joins  |                  | 表连接,MongoDB不支持                |
| primary key  | primary key      | 主键,MongoDB自动将_id字段设置为主键 |



# mongo shell

mongo shell是用来操作mongoDB数据库的JavaScript语法命令行工具，路径在`bin/mongo.exe`。

## 常用命令

- `print()`  ：打印控制台

- `exit` ： 关闭mongo shell

  



# 库

- `show dbs`：显示mongoDB所有的数据库

  系统默认会有三个库

  - **admin**： 从权限的角度来看，这是"root"数据库。要是将一个用户添加到这个数据库，这个用户自动继承所有数据库的权限。一些特定的服务器端命令也只能从这个数据库运行，比如列出所有的数据库或者关闭服务器。
  - **local:** 这个数据永远不会被复制，可以用来存储限于本地单台服务器的任意集合
  - **config**: 当Mongo用于分片设置时，config数据库在内部使用，用于保存分片的相关信息。



- `use name`：name表示库名，如果数据库不存在，则创建数据库，否则切换到指定数据库。



- `db` ：显示当前数据库对象



- `db.dropDatabase()`：删除当前数据库



# 集合

集合类似关系型数据库的表用来存储行，集合用来存储文档

- `show collections`：显示当前数据库下所有的集合
- `db.createCollection(collectionName[, options])` : 创建集合（collectionName）

- `db.collectionName.drop()` : 删除当前库下指定集合（collectionName）



# 文档

文档类似sql中的行，用来存储数据

MongoDB 中可以使用的类型如下表所示：

| 类型                    | 枚举 | **备注**         |
| :---------------------- | :--- | :--------------- |
| Double                  | 1    |                  |
| String                  | 2    |                  |
| Object                  | 3    |                  |
| Array                   | 4    |                  |
| Binary data             | 5    |                  |
| Undefined               | 6    | 已废弃。         |
| Object id               | 7    |                  |
| Boolean                 | 8    |                  |
| Date                    | 9    |                  |
| Null                    | 10   |                  |
| Regular Expression      | 11   |                  |
| JavaScript              | 13   |                  |
| Symbol                  | 14   |                  |
| JavaScript (with scope) | 15   |                  |
| 32-bit integer          | 16   |                  |
| Timestamp               | 17   |                  |
| 64-bit integer          | 18   |                  |
| Min key                 | 255  | Query with `-1`. |
| Max key                 | 127  |                  |



## _id

mongoDB存储的是文档，每篇文档中的唯一主键是`_id`，除了数组之外的所有mongoDB数据类型都可以作为主键，甚至可以使用另一篇文档作为这篇文档的主键（复合主键），由mongoDB默认生成的文档主键是ObjectId。

ObjectId 唯一主键，可以很快的去生成和排序，包含 12 bytes，含义是：

- 前 4 个字节表示创建 **unix** 时间戳,格林尼治时间 **UTC** 时间，比北京时间晚了 8 个小时
- 接下来的 3 个字节是机器标识码
- 紧接的两个字节由进程 id 组成 PID
- 最后三个字节是随机数



## 命令

- 创建文档

  `db.collection.insertOne()`

