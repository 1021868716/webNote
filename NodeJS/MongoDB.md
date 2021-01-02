# mongoDB

关系型数据库通过表格的形式存储数据，可以通过列来对不同的表格建立联系。

mongoDB是非关系型数据库，非关系型数据库不是表存储行，可以存储多种数据结构，例如redis存储键值对，mongoDB存储BSON文档，还有其他的存储对象，图等。

SQL是使用表来存储行，而mongoDB是使用集合（ collection）来存储文档（document）。

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

- `print()`  ：打印控制台

- `exit` ： 退出mongo shell

- cls：清屏

# 库

- `show dbs`：显示mongoDB所有的数据库

  系统默认会有三个库

  - **admin**： 从权限的角度来看，这是"root"数据库。要是将一个用户添加到这个数据库，这个用户自动继承所有数据库的权限。一些特定的服务器端命令也只能从这个数据库运行，比如列出所有的数据库或者关闭服务器。
  - **local:** 这个数据永远不会被复制，可以用来存储限于本地单台服务器的任意集合
  - **config**: 当Mongo用于分片设置时，config数据库在内部使用，用于保存分片的相关信息。



- `use <dbName>`：dbname表示库名，如果数据库不存在，则创建数据库，否则切换到指定数据库。



- `db` ：显示当前数据库对象



- `db.dropDatabase()`：删除当前数据库



# 集合

集合类似关系型数据库的一张表用来存储多条行数据，mongoDB中一个集合用来存储多个文档

- `show collections`：显示当前数据库下所有的集合



- `db.createCollection(<collection>, options)` : 创建集合（collectionName）

  collection名需要为字符串形式传入

  ```shell
  db.createCollection('people')
  ## 创建一个名叫people的集合
  ```

  

- `db.<collection>.drop()` : 删除当前库下指定集合（collection）





# 文档

文档类似sql中的行，用来存储数据，文档中的类似JSON的存储数据结构被称为BSON。

BSON是一种数据交换格式，主要被用作MongoDB数据库中的数据存储和网络传输格式。它是一种二进制表示形式，能用来表示简单数据结构、关联数组（MongoDB中称为“对象”或“文档”）以及MongoDB中的各种数据类型。BSON之名缘于JSON，含义为Binary JSON（二进制JSON）。

BSON文档（对象）由一个有序的元素列表构成。每个元素由一个字段名、一个类型和一个值组成。BSON的类型名义上是JSON类型的一个超集（JSON没有date或字节数组类型），但一个例外是没有像JSON那样的通用“数字”（number）类型。

BSON字段名为字符串。类型包括：

| 类型                    | 枚举值 | **备注**                                                     |
| :---------------------- | :----- | :----------------------------------------------------------- |
| Double                  | 1      | 双精度浮点值，用于存储浮点值。                               |
| String                  | 2      | BSON 中字符串是 UTF-8 编码                                   |
| Object                  | 3      | 用于内嵌文档                                                 |
| Array                   | 4      | 用于将数组或列表或多个值存储为一个键                         |
| Binary data             | 5      | 二进制数据。用于存储二进制数据。                             |
| Undefined               | 6      | 已废弃                                                       |
| Object id               | 7      | 主键                                                         |
| Boolean                 | 8      | 布尔值                                                       |
| Date                    | 9      | 日期时间。用 UNIX 时间格式来存储当前日期或时间。创建 Date 对象，传入年月日信息。 |
| Null                    | 10     | 用于创建空值                                                 |
| Regular Expression      | 11     | 正则类型，用于存储正则表达式。                               |
| JavaScript              | 13     |                                                              |
| Symbol                  | 14     | 符号。基本上等同于字符串类型，但它一般用于采用特殊符号类型的语言 |
| JavaScript (with scope) | 15     |                                                              |
| 32-bit integer          | 16     | 32位整型数值。                                               |
| Timestamp               | 17     | 时间戳。记录文档修改或添加的具体时间。                       |
| 64-bit integer          | 18     | 64位整型数值。                                               |
| Min key                 | 255    | 将一个值与 BSON（二进制的 JSON）元素的最低值相对比。         |
| Max key                 | 127    | 将一个值与 BSON（二进制的 JSON）元素的最高值相对比。         |

与JSON相比，BSON着眼于提高存储和扫描效率。BSON文档中的大型元素以长度字段为前缀以便于扫描。在某些情况下，由于长度前缀和显式数组索引的存在，BSON使用的空间会多于JSON。



## 返回信息

对文档进行一次操作后，mongo shell会返回一个结果文档（成功或者失败），包含了此次操作的结果信息。可能包含以下字段。

insertOne/Many插入文档成功返回的一个无名文档，该文档包含以下字段

- "acknowledged"：boolean，安全写级别是否被启用
- "insertedId"：成功插入单篇文档时，返回该篇文档主键，插入多篇文档返回insertedIds主键数组



insertOne插入文档失败，返回writeError文档

- writeError()：插入单篇文档产生错误，返回的错误信息对象文档



insertMany插入文档失败，返回BulkWriteError文档

- BulkWriteError()：如果插入多篇则返回的错误对象文档
  - "writeErrors"：array，所有错误信息对象文档的数组
  - "nXXXXX"：number，执行操作的统计信息，例如nInserted是成功插入文档的数量



使用insert插入单篇文档成功/失败，返回WriteResult文档

- WriteResult()
  - "nInserted"：成功插入文档数量
  - "writeError"：如果插入失败才有这个字段，本次插入的错误信息对象



使用insert插入多篇文档成功/失败，返回BulkWriteResult文档

- BulkWriteResult()
  - "writeErrors"：array，所有错误信息对象的数组
  - "nXXXXX"：number，执行操作的统计信息，例如nInserted是成功插入文档的数量



## _id

mongoDB存储的是文档，每篇文档中的唯一主键是`_id`，除了数组之外的所有mongoDB数据类型都可以作为主键，甚至可以使用另一篇文档作为这篇文档的主键（复合主键），由mongoDB默认生成的文档主键是ObjectId。

ObjectId生成的唯一主键，可以很快的去生成和排序，包含 12 bytes，含义是：

- 前 4 个字节表示创建 **unix** 时间戳,格林尼治时间 **UTC** 时间，比北京时间晚了 8 个小时
- 接下来的 3 个字节是机器标识码
- 紧接的两个字节由进程 id 组成 PID
- 最后三个字节是随机数



可以使用以下命令操作ObjectId

- `ObjectId()`：生成一个ObjectId主键

- `ObjectId(<ObjectId>).getTimestamp`：提取该ObjectId的时间戳



## 复合主键

以一个文档作为文档主键

```shell
## 插入文档的主键还是一篇文档
db.people.insert(
  {
  	_id: {name: "www", age: 30},
  	name: "abc",
  	age: 20
  }
)


## 复合主键依然要满足主键的唯一性，插入一条主键相同的文档
db.people.insert(
  {
  	_id: {name: "www", age: 30},
  	name: "efg",
  	age: 40
  }
)
## 报错，复合主键相同


## 但是调换复合主键中字段顺序就可以算作一个不同的复合主键
db.people.insert(
  {
  	_id: {age: 30, name: "www"},
  	name: "efg",
  	age: 40
  }
)
## 插入成功
```





# 新增文档

以下三种插入方式（insertOne，insertMany，insert）的区别有：

1. 报错信息不同
2. insertOne/Many不支持`db.<collection>.explain()`命令



## insertOne

- 插入一篇文档：`db.<collection>.insertOne()`

```shell
db.<collection>.insertOne(
  <document>,
  {
	writeConcern: <document> ## 安全写级别
  }
)
```

options中的writeConcern，安全写级别，比嗾使判断一次数据库写入操作是否成功，安全写级别越高，丢失数据的风险越低，但是写入操作的延迟就会越高。不提供writeConcern，则会使用默认的安全写级别。

如果collection集合未被创建会自动创建。



```shell
db.people.insertOne(
  {
  	_id: 'account', ## 手动设置主键
  	name: 'abc',
  	age: 20
  }
)
## 向people集合中插入一条文档，如果people集合未被创建会自动创建
## mongo shell返回 { "acknowledged" : true, "insertedId" : "account" }
```

如果插入操作成功，mongo shell会返回一个文档，包含两个字段，acknowledged字段为true表示安全写级别被启用，insertedId字段是显示插入文档的文档主键（insertMany时返回数组）。

如果插入操作失败（例如主键重复），则会抛出错误，mongo shell会显示错误信息。可以使用try/catch捕获错误对象并打印。

```shell
try {
  db.people.insertOne(
    {
  	  _id: 'account',
  	  name: 'efg',
  	  age: 50
    }
  )
} catch (e) {
    print(e)
}


## 抛出以下错误文档，主键重复
WriteError({
        "index" : 0,
        "code" : 11000,
        "errmsg" : "E11000 duplicate key error collection: test.people index: _id_ dup key: { _id: \"account\" }",
        "op" : {
                "_id" : "account",
                "name" : "efg",
                "age" : 50
        }
})
```



## insertMany

- 插入多篇文档：`db.<collection>.insertMany()`

  ```shell
  db.<collection>.insertOne(
    [<document1>, <document2>, ...]
    {
  	writeConcern: <document>,
  	ordered: <boolean> ## 是否顺序写入，默认true
    }
  )
  ```

  options中的ordered表示，是否要按顺序来写入这些文档，如果设置为false，mongoDB则可以打乱文档写入顺序，提高写入性能。

  顺序写入时，如果遇到错误，操作立即退出，错误文档之后的文档无论正确错误，都不会往集合中继续写入。

  乱序写入时，如果遇到错误，其他正确的文档都会成功写入，错误的文档则不会写入并在控制台抛出错误。





## insert

- 插入一篇或者多篇文档：`db.<collection>.insert()`  / `db.<collection>.save()`

  可以传入单篇文档或者文档数组，options和insertMany一致。当写入操作成功后，mongo shell返回的成功对象的nInserted字段表示成功插入的文档篇数。save内部就是调用insert，两者等同。







# 查询文档

查询文档的基本命令是find，使用find命令返回的结果时是游标

`db.<collection>.find(<query>, <projection>)` ： 

​	查询集合中的文档，query表示查询条件，projection表示对读取的结果进行投射（只展示规定的字段）

​	使用`db.<collection>.find().pretty()`，可以以树型格式化展示查询出来的文档



query可以添加筛选条件

并（and），可以直接在上一个条件后面进行添加

```shell
db.people.find({name: "abc", age: 20})
db.people.find({"name": "abc", age: 20}) ## 键名可以写成字符串形式

## 根据复合主键的属性来进行查询，复合主键查询则要将键名写成字符串形式
db.people.find({"_id.name": "www"})
```

或（or），则需要在query中使用`$or`数组

```
$or: [{key1: value1}, {key2:value2}]
```

```shell
db.people.find({ name: "abc", $or: [{age: 20}, {age: 40}] })
## 筛选name为abc，age为20或者40的文档
```





## 操作符

操作符可以用来复杂的文档筛选

类比SQL 数据，通过下表可以更好的理解 MongoDB 的条件语句查询：

| 操作     | 格式                     | 范例                               | SQL               |
| :------- | :----------------------- | :--------------------------------- | :---------------- |
| 等于     | `{<key>:<value>`}        | `db.col.find({"by":"菜鸟教程"})`   | where by = '菜鸟' |
| 小于     | `{<key>:{$lt:<value>}}`  | `db.col.find({"likes":{$lt:50}}))` | where likes < 50  |
| 小于等于 | `{<key>:{$lte:<value>}}` | `db.col.find({"likes":{$lte:50}})` | where likes <= 50 |
| 大于     | `{<key>:{$gt:<value>}}`  | `db.col.find({"likes":{$gt:50}})`  | where likes > 50  |
| 大于等于 | `{<key>:{$gte:<value>}}` | `db.col.find({"likes":{$gte:50}})` | where likes >= 50 |
| 不等于   | `{<key>:{$ne:<value>}}`  | `db.col.find({"likes":{$ne:50}})`  | where likes != 50 |

对字符串进行比较，则会根据首字母排序得出结果