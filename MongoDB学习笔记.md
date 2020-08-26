MongoDB系统结构

NoSQL = Not only SQL，没有结构化的存储要求，架构更加灵活

NoSQL数据库四大家族：列存储Hbase、键值存储Redis、图像存储Neo4j、文档存储MongoDB

MongoDB是一个基于分布式文件存储的数据库，由C++编写，可为WEB应用提供可扩展、高性能、易部署的数据存储方案

##### MongoDB体系结构

###### 体系架构图

![image-20200822151737588](C:\Users\x1850\AppData\Roaming\Typora\typora-user-images\image-20200822151737588.png)

###### MongoDB VS RDBMS

| RDBMS           | MongoDB                  |
| --------------- | ------------------------ |
| database数据库  | database数据库           |
| table表         | collection集合           |
| row行           | document文档             |
| column列        | field字段                |
| index索引       | index索引                |
| join主外键关联  | embeded Document嵌套文档 |
| primary key主键 | primary key主键          |

###### BSON

- 什么是BSON（Binary JSON）

  BSON是一种类JSON的二进制形式的存储格式，和JSON一样，支持内嵌的文档对象和数组对象，但是BSON还支持一些JSON没有的数据类型，比如Date和Binary Data

  优点：无结构化，灵活性高

  缺点：空间利用率不理想

  BSON三个特点：轻量级、可遍历性、高效性

- MongoDB中Document支持的数据类型

  | 数据类型      | 说明                     | 样例                                                |
  | ------------- | ------------------------ | --------------------------------------------------- |
  | String        | 字符串                   | {"name":"xcl"}                                      |
  | Integer       | 整型数值                 | {“age”:18}                                          |
  | Boolean       | 布尔值                   | {"isDel":true}                                      |
  | Double        | 双精度浮点值             | {key:3.14}                                          |
  | ObjectId      | 对象ID(用于创建文档的ID) | {_id:new ObjectId()}                                |
  | Array         | 数组                     | {arr:["z","b"]}                                     |
  | Timestamp     | 时间戳                   | {ts:new Timestamp()}                                |
  | Object        | 内嵌文档                 | {o:{foo:"bar"}}                                     |
  | Null          | 空值                     | {key:null}                                          |
  | Date或ISODate | 时间                     | {birth:new Date()}                                  |
  | Code          | 代码(可以包含JS代码)     | {x:function(){}}                                    |
  | File          | 文件                     | GridFS 用两个集合来存储一个文件：fs.ﬁles与fs.chunks |

###### linux中安装MongoDB

```
1.官网下载对应的MongoDB 然后上传到Linux虚拟机 
2.将压缩包解压  tar -zxvf MongoDB-linux-x86_64-4.1.3.tgz 
3.进入安装目录，启动    默认配置文件启动./bin/mongod 或指定配置文件启动 ./bin/mongod -f my.conf


配置文件样例:   
dbpath=/data/mongo/    //数据库目录
port=27017    //端口号
bind_ip=0.0.0.0    //监听的ip地址，默认全部可访问
fork=true    //是否已后台进程方式启动
logpath = /data/mongo/MongoDB.log    //日志路径
logappend = true    //是否追加写日志
auth=false //是否启用用户密码登录

```

###### mongo shell启动

```
启动mongo shell    ./bin/mongo  
指定主机和端口的方式启动    ./bin/mongo --host=主机IP --port=端口
```

###### MongoDB GUI工具

- MongoDB  Compass Community

  MongoDB Compass  Community由MongoDB开发人员开发，具有完整的CRUD功能并提供可视方式。借助内置模式可视化，用户可以分析文档并显示丰富的结构。为了监控服务器的负载，它提供了数据库操作的实时统计信息

- NoSQLBooster(mongobooster)

  NoSQLBooster是一个跨平台，它带有一堆mongodb工具来管理数据库和监控服务器。这个Mongodb 工具包括服务器监控工具，Visual Explain  Plan，查询构建器，SQL查询，ES2017语法支持等等

#### MongoDB命令

##### 基本操作

```
查看数据库 show dbs;
切换数据库，若不存在则新建 use 数据库名;
创建集合 db.createCollection("集合名");
查看集合 show tables or show collections;
删除集合 db.集合名.drop();
删除当前数据库 db.dropDatabase();
```

##### 集合数据操作CRUD

- 数据添加

  ```
  //插入单个文档
  db.集合名.insert(文档)
  //批量插入
  db.集合名》insert([文档，文档])
  
  注：_id字段若没有人为指定，系统会自动生成
  _id是ObjectId类型的一个12个字节的BSON数据，格式如下：
  前四个字节表示时间戳，可用ObjectId("对象id").getTimestamp获取
  接下来的3个字节是机器标识码
  紧接的2个字节是PID进程Id
  最后3个字节是随机数
  ```

- 数据查询

  ```
  //查询全部数据
  db.集合名.find()
  ```

  比较条件查询

  | 操作     | 例子                           |
  | -------- | ------------------------------ |
  | 等于     | db.col.ﬁnd({字段名:值})        |
  | 大于     | db.col.ﬁnd({字段名:{$gt:值}})  |
  | 小于     | db.col.ﬁnd({字段名:{$lt:值}})  |
  | 大于等于 | db.col.ﬁnd({字段名:{$gte:值}}) |
  | 小于等于 | db.col.ﬁnd({字段名:{$lte:值}}) |
  | 不等于   | db.col.ﬁnd({字段名:{$ne:值}})  |

  逻辑条件查询

  ```
  //and
   db.集合名.find({key1:value1, key2:value2})
   db.集合名.find({$and:[{key1:value1}, {key2:value2}]})
   
  //or
   db.集合名.find({$or:[{key1:value1}, {key2:value2}]})
   
  //not
   db.集合名.find({key:{$not:{$操作符:value}})
  ```

  分页查询

  ```
  db.集合名.ﬁnd({条件}).sort({排序字段:排序方式})).skip(跳过的行数).limit(一页显示多少数据)
  
  ```

- 数据更新

  ```
  $set ：设置字段值 
  $unset  :删除指定字段 
  $inc：对修改的值进行自增 
  db.集合名.update(   
  	<query>,   //update的查询条件，类似sql update查询内where后面的
  	<update>,   //update的对象和一些更新的操作符（如$set,$inc...）等，也可以理解为sql update中 set后面的
  	{     
  		upsert: <boolean>,  //可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认 是false，不插入。 
  		multi: <boolean>,     //可选，MongoDB 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查 出来多条记录全部更新
  		writeConcern: <document>   可选，用来指定mongod对写操作的回执行为比如写的行为是否需要确认
  	} 
  )
  
  db.集合名.update({条件},{$set:{字段名:值}},{multi:true})
  ```

- 数据删除

  ```
  db.collection.remove(   
  	<query>,   //条件
  	{     
  		justOne: <boolean>,    //设为true或1，则只删除一个文档，如果不设置该参数或设为false，则删除所有匹配的文档
          writeConcern: <document>  //可选，用于指定对写操作的回执行为
       } 
  )
  ```

##### 聚合操作

- 聚合操作简介

  聚合是MongoDB的高级查询语言，其可以通过多个文档生成新的单个文档

  MongoDB不允许Pipeline的单个聚合操作占用过多的系统内存，如果一个聚合操作消 耗20%以上的内存，那么MongoDB直接停止操作，并向客户端输出错误消息

- 聚合操纵分类

  - 单目的聚合操作

    单目的聚合命令常用的有：count() 和 distinct()

    ```
    db.集合名.find({}).count()
    ```

  - 聚合管道

    聚合管道主要用于统计数据，并返回计算后的数据结果

    表达式：处理输入文档并输出

    | 表达式    | 描述                                         |
    | --------- | -------------------------------------------- |
    | $sum      | 计算总和                                     |
    | $avg      | 计算平均值                                   |
    | $min      | 获取集合中最小值                             |
    | $max      | 获取集合中最大值                             |
    | $push     | 在结果文档中插入值到一个数组中               |
    | $addToSet | 在结果文档中插入值到一个数组中，但数据不重复 |
    | $ﬁrst     | 排序后获取第一个文档                         |
    | $last     | 排序后获取最后一个文档                       |

    MongoDB 中使用 db.COLLECTION_NAME.aggregate([{},...]) 方法来构建和使用聚合管道，每个 文档通过一个或者多个阶段（stage）组成的管道，经过一系列的处理，输出相应的结果。 

    常用操作：

    - $group：分组
    - $project：修改文档的结构
    - $match：过滤数据
    - $limit：限制结果集数目
    - $skip：跳过指定行数的文档
    - $sort：排序
    - $geoNear：输出接近某一地理位置的有序文档

    ```
    db.集合名.aggregate( [{$group:{_id: "$city",count:{$sum : 1}}}, {$match:{count:{$gt:1}}} ]) 
    ```

  - MapReduce编程模型

    MapReduce是一种计算模型，可以将大量的工作（数据）分解（MAP）执行，然后再将结果合并

    ```
    db.collection.mapReduce(   
    	function() {emit(key,value);},  //map函数：遍历集合中的文档，将key，value传递给reduce函数处理
        function(key,values) {return reduceFunction},   //reduce函数   
        {      
        	out: collection,      //结果
        	query: document,     //查询条件
            sort: document,      //排序
            limit: number,      //限制结果集
            finalize: <function>,      //可以对reduce输出结果进行修改
            verbose: <boolean>   //是否包含结果信息中的时间信息，默认false
         } 
    )
    
    //计算工资高于15000的平均值，结果保存在cityAvgSal变量中
    db.集合名.mapReduce(   
    	function() { emit(this.city,this.expectSalary); },  
        function(key, value) {return Array.avg(value)},  
        {        
        	query:{expectSalary:{$gt: 15000}},      
        	out:"cityAvgSal"  
         } 
     )
    
    ```

    

#### MongoDB索引Index

##### 什么是索引

索引是为了提高查询效率的一种存储结构。默认情况下Mongo在一个集合（collection）创建时，自动地对创建名为_id的唯一索引

##### 索引类型

- 单键索引

  ```
   db.集合名.createIndex({"字段名":排序方式}) 
  ```

  特殊的单键索引：TTL（Time To Live）过期索引

  TTL索引是MongoDB中一种特殊的索引， 可以支持文档在一定时间之后自动过期删除，目前TTL索引 只能在单字段上建立，并且字段类型必须是日期类型

  ```
  db.集合名.createIndex({"日期字段":排序方式}, {expireAfterSeconds: 秒数})
  ```

- 复合索引

  在多个字段上建立索引，需考虑字段顺序和排序方式

  ```
  db.集合名.createIndex( { "字段名1" : 排序方式, "字段名2" : 排序方式 } )
  ```

- 多键索引

  如果属性中包含数组，MongoDB支持对数组中的每一个元素创建索引，多键索引支持strings、numbers和nested documents

- 地理空间索引

  对地理空间创建索引。有两类：

  2dsphere：用于存储和查找球面上的点 

   2d：用于存储和查找平面上的点

  ```
  db.集合名.insert(   
  	{    
      	loc : { type: "Point", coordinates: [ 116.482451, 39.914176 ] },     		 name: "大望路地铁",    
          category : "Parks"   
       }
  )
  
  //创建索引
  db.集合名.ensureIndex( { loc : "2dsphere" } )
  
  //查看指定坐标5公里之内的文档
  db.集合名.find(
  {    
  	"loc" : {         
  		"$geoWithin" : {          
  			"$center":[[116.482451,39.914176],0.05]         
  		}   
         } 
        }
     )
  
  
  ```

- 全文索引

  MongoDB提供了针对string内容的文本查询，Text Index支持任意属性值为string或string数组元素的 索引查询。一个集合仅支持最多一个Text Index

  ```
  //创建索引
  db.集合.createIndex({"字段": "text"}) 
  //查看包含coffee的文档
  db.集合.find({"$text": {"$search": "coffee"}})
  ```

- 哈希索引

  对属性的哈希值进行索引。注：哈希索引只适合等值查询，不适用于范围查询

  ```
  db.集合.createIndex({"字段": "hashed"})
  ```

##### 索引explain分析

- 索引管理

  创建索引并后台运行

  ```
  db.集合名.createIndex({name:1},{background,true}) //在name字段上升序
  ```

  获取某个集合的索引

  ```
  db.集合名.getIndexs()
  ```

  获取集合索引大小

  ```
  db.集合名.totalIndexSize()
  ```

  索引的重建

  ```
  db.集合名.reIndex()
  ```

  索引的删除

  ```
  //删除所有索引
  db.集合名.dropIndexs()
  //删除指定索引
  db.集合名.dropIndex("name_1")
  
  注：_id索引无法删除
  ```

- explain分析

  explain()接收不同的参数，通过设置不同参数我们可以查看更详细的查询计划

  1. queryPlanner：queryPlanner是默认参数

     | 参数                           | 含义                             |
     | ------------------------------ | -------------------------------- |
     | plannerVersion                 | 查询计划版本                     |
     | namespace                      | 命名空间=数据库.集合             |
     | indexFilterSet                 | 是否有索引过滤                   |
     | parsedQuery                    | 查询条件                         |
     | winningPlan                    | 被选中的执行计划                 |
     | winningPlan.stage              | 查询方式                         |
     | winningPlan.inputStage         | 描述子查询方式                   |
     | winningPlan.stage的child stage | 如果是IXSCAN，表示index scanning |
     | winningPlan.keyPattern         | 所扫描的index内容                |
     | winningPlan.indexName          | 选中的索引                       |
     | winningPlan.isMultiKey         | 是否为多键索引                   |
     | winningPlan.direction          | 查询顺序，升序或逆序             |
     | ﬁlter                          | 过滤条件                         |
     | winningPlan.indexBounds        | 索引范围                         |
     | rejectedPlans                  | 被拒绝的执行计划的详细返回       |
     | serverInfo                     | 服务器信息                       |

  2. executionStats：executionStats会返回执行计划的一些统计信息

     | 参数                                   | 含义                                     |
     | -------------------------------------- | ---------------------------------------- |
     | executionSuccess                       | 是否执行成功                             |
     | nReturned                              | 返回的文档数                             |
     | executionTimeMillis                    | 执行耗时                                 |
     | totalKeysExamined                      | 索引扫描次数                             |
     | totalDocsExamined                      | 文档扫描次数                             |
     | executionStages                        | 执行的状态                               |
     | stage                                  | 扫描方式                                 |
     | executionTimeMillisEstimate            | 检索文档获得数据所需时间                 |
     | inputStage.executionTimeMillisEstimate | 该查询扫描文档index所需时间              |
     | works                                  | 工作单元数，一个查询会分解成小的工作单元 |
     | advanced                               | 优先返回的结果数                         |
     | docsExamined                           | 文档检索数目                             |
  
   stage的类型：
  
   - COLLSCAN 全表扫描
     - IXSCAN 索引扫描
   - FETCH 根据索引去检索指定document
     - SHARD_MERGE 将各个分片返回数据进行合并
     - SORT 排序
     - LIMIT 限制结果集数目
     - SKIP 跳过指定行数
     - IDHACK 针对_id进行查询
     - SHARDING_FILTER 对分片数据进行查询
     - COUNT 总数
     - TEXT 全文检索
     - PROJECTION 限定返回字段的时候stage的返回
  
  3. allPlansExecution：用来获取所有执行计划
  
     queryPlanner参数和executionStats的拼接 

##### 慢查询分析

```
1.开启内置的查询分析器，记录读写操作效率
db.setProfilingLevel(n,m)
n的取值可选0，1，2
0不记录 
1记录慢查询，m定义慢查询时间的阈值，单位为ms
2记录所有的读写操作

2.查询监控结果
db.system.profile.find().sort({millis:-1}).limit(3)

3.分析慢查询
应用程序设计不合理、不正确的数据模型、索引的设计

4.解读explain
```

##### 索引底层实现原理

mongoDB使用B-树，B-树是一种自平衡的搜索树

![image-20200825202911431](C:\Users\x1850\AppData\Roaming\Typora\typora-user-images\image-20200825202911431.png)

B-树的特点：

1. 多路非二叉树
2. 每个节点即保存数据又保存索引
3. 搜索时类似二分查找

mysql索引使用B+树，B+树是B-树的变种：

![image-20200825203155942](C:\Users\x1850\AppData\Roaming\Typora\typora-user-images\image-20200825203155942.png)

B+树的特点：

1. 多路非二叉树
2. 只有叶子节点保存数据
3. 搜索时类似二分查找
4. 叶子之间使用指针进行连接

B+ VS B-：

1. B+树的相邻指针增加了区间访问性，适用于范围查询

   B-由于每个节点即保存数据又保存索引，适合随机读写

2. B+树更适合外部存储，由于节点内只保存索引，所以每个节点所能保存的索引更多

   B-不能好好利用磁盘预读的功能

   

#### MongoDB实战

#####  MongoDB的适用场景

- 网站数据
- 缓存
- 大尺寸、低价值的数据
- 高伸缩性的场景
- 文档数据存储

##### MongoDB的行业具体应用场景

- 游戏场景
- 物流场景
- 社交场景
- 物联网场景
- 直播

##### java访问MongoDB

- 引入maven坐标
- 借助MongoClient客户端

```
<dependency>    
	<groupId>org.mongodb</groupId>    
	<artifactId>mongo-java-driver</artifactId>   
    <version>3.10.1</version> 
</dependency>

//文档添加
 MongoClient mongoClient = new MongoClient("192.168.211.133", 37017); 
 MongoDatabase database = mongoClient.getDatabase("lg_resume"); MongoCollection<Document> collection = database.getCollection("lg_resume_preview"); Document document = Document.parse(     "{name:'lisi',city:'bj',birth_day:new ISODate('2001-0801'),expectSalary:18000}"); 
 collection.insertOne(document ); 
 mongoClient.close(); 
 
```



##### Spring访问MongoDB

- 引入maven依赖

- 配置MongoTemplate

- 注入MongoTemplate

  ```
  <dependency>   
  	<groupId>org.springframework.data</groupId>    
  	<artifactId>spring-data-mongodb</artifactId>    	
      <version>2.0.9.RELEASE</version> 
  </dependency>
  
  <?xml version="1.0" encoding="UTF-8"?> <beans xmlns="http://www.springframework.org/schema/beans"       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"       xmlns:mongo="http://www.springframework.org/schema/data/mongo"       xsi:schemaLocation="    http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
     http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd    http://www.springframework.org/schema/data/mongo http://www.springframework.org/schema/data/mongo/spring-mongo.xsd">     <!-- 构建MongoDb工厂对象 -->    <mongo:db-factory id="mongoDbFactory"          client-uri="mongodb://192.168.211.133:37017/lg_resume">    </mongo:db-factory>    <!--  构建 MongoTemplate 类型的对象 -->    <bean id="mongoTemplate" class="org.springframework.data.mongodb.core.MongoTemplate">         <constructor-arg index="0" ref="mongoDbFactory"></constructor-arg>    </bean>    <!-- 开启组件扫描 -->    <context:component-scan base-package="com.lagou"></context:component-scan> </beans> 
  
  @Autowired protected MongoTemplate mongoTemplate;
  ```

##### SpringBoot访问MongoDB

- 引入maven依赖

  ```
  <dependency>   
  	<groupId>org.springframework.boot</groupId>   
      <artifactId>spring-boot-starter-data-mongodb</artifactId>   
      <version>2.2.2.RELEASE</version> 
  </dependency>
  ```

- 配置application.properties

  ```
  spring.data.mongodb.host=192.168.211.133 
  spring.data.mongodb.port=37017 
  spring.data.mongodb.database=lg_resume
  ```

- 注入MongoTemplate

  ```
  @Autowired 
  protected MongoTemplate mongoTemplate;
  ```

#### MongoDB架构

##### MongoDB逻辑架构

![image-20200825211935975](C:\Users\x1850\AppData\Roaming\Typora\typora-user-images\image-20200825211935975.png)

从上至下分别为：

客户端

mongoDB查询语言

mongoDB数据模型

可插拔式存储引擎

##### MongoDB数据模型

- 内嵌

  内嵌的方式指的是将相关联的数据保存在同一个文档结构之中

  使用场景：

  - 数据对象之间有包含关系，一般为一对多或一对一
  - 需要经常一起读取的数据
  - 有map-reduce/aggregation需求的数据

- 引用

  存储数据引用来实现两个不同文档之间的关联，应用程序通过解析数据引用来访问相关数据

  使用场景：

  - 当内嵌会导致很多数据的重复
  - 需要表达比较复杂的多对对关系
  - 大型层次结果数据集

##### MongoDB存储引擎

存储引擎负责管理数据在硬盘和内存中的存储。mongoDB支持的存储引擎有MMAPv1，WiredTiger和InMemory

- InMemory：将数据存储在内存中，只将少量的元数据和诊断日志存储到硬盘中

- MMAPv1：

  mongoDB3.2版本之前的存储引擎

- WiredTiger：

  mongoDB3.2默认的存储引擎

#### MongoDB集群高可用

##### MongoDB主从复制架构

主从架构中，主节点的操作记录到oplog（operation log）中，oplog存储在系统数据库local的oplog.$main集合中，这个集合中的每个文档都代表主节点上执行的一个操作。从服务器定期从主服务器中获取oplog记录，然后在本机上执行

对于存储oplog的集合，MongoDB采用固定集合（新操作会覆盖旧操作数据）

缺点：主从架构没有自动故障转移功能，需要指定master和slave

mongoDB4.0后不再支持主从复制

##### 复制集

- 什么是复制集

  ![image-20200825220857609](C:\Users\x1850\AppData\Roaming\Typora\typora-user-images\image-20200825220857609.png)

  复制集是由2台或以上拥有相同数据集的mongdb实例组成的集群

  节点分类：primary主节点、secondary从节点、仲裁节点

  复制集提供了数据的冗余备份，提高了数据的可用性，保证数据的安全性

- 为什么要使用复制集

  - 高可用
  - 灾难恢复
  - 功能隔离

- 复制集集群架构原理

  主节点记录所有改变数据库状态的操作，这些记录保存在oplog中。各个从节点通过oplog来保持本地的数据与主节点一致

  oplog具有幂等性

  oplog的组成结构

  ```
  { 
  	"ts" : Timestamp(1446011584, 2), 
  	"h" : NumberLong("1687359108795812092"), 
  	"v" : 2, 
  	"op" : "i",
      "ns" : "test.nosql", 
      "o" : { "_id" : ObjectId("563062c0b085733f34ab4129"), "name" : "mongodb", "score" : "10"} 
  } 
      ts：操作时间，当前timestamp + 计数器，计数器每秒都被重置
      h：操作的全局唯一标识
      v：oplog版本信息
      op：操作类型    
      	i：插入操作  
          u：更新操作   
          d：删除操作   
          c：执行命令（如createDatabase，dropDatabase） 
      n：空操作，特殊用途 
      ns：操作的集合 
      o：操作内容 
      o2：更新查询条件,仅update操作包含该字段
  
  ```

  复制集数据同步分为初始化同步和keep复制同步。初始化同步指全量从主节点同步数据，如果Primary 节点数据量比较大同步时间会比较长。而keep复制指初始化同步过后，节点之间的实时同步一般是增量同步

  初始化同步有以下两种情况会触发：

  - secondary第一次加入
  -  Secondary落后的数据量超过了oplog的大小，这样也会被全量复制

  主节点选举：

  主节点的选举基于心跳检测，一个复制集N个节点中的任意两个节点维持心跳，每个节点维护其他N-1个节点的状态

  主节点选举触发事件：

  1. 第一次初始化一个复制集
  2. secondary节点权重高于primary节点，发起替换选举
  3. secondary节点发现集群中没有primary时，发起选举
  4. primary节点不能访问到大部分成员时主动降级

  ![image-20200825223810537](C:\Users\x1850\AppData\Roaming\Typora\typora-user-images\image-20200825223810537.png)

  

  当触发选举时，secondary节点尝试将自身选举为primary。主节点选举是一个二阶段协议+多数派协议的过程

- 复制集搭建

  ![image-20200825231644991](C:\Users\x1850\AppData\Roaming\Typora\typora-user-images\image-20200825231644991.png)

  主节点配置

  ```
  # 数据存放路径
  dbpath=/data/mongo/data/shard1_37011
  # 端口号
  port=37011
  # 任意ip均可访问
  bind_ip=0.0.0.0
  # 以后台方式启动
  fork=true
  # 日志路径
  logpath=/data/mongo/logs/shard1_37011/mongoDB.log
  # 日志以追加方式写入
  logappend=true
  # 复制集名称 名称相同的一组节点为一个复制集
  replSet=shard1
  ```

  从节点配置

  ```
  # 数据存放路径
  dbpath=/data/mongo/data/shard1_37011
  # 端口号
  port=37011
  # 任意ip均可访问
  bind_ip=0.0.0.0
  # 以后台方式启动
  fork=true
  # 日志路径
  logpath=/data/mongo/logs/shard1_37011/mongoDB.log
  # 日志以追加方式写入
  logappend=true
  # 复制集名称 名称相同的一组节点为一个复制集
  replSet=shard1
  ```

  初始化节点配置：

  启动以上配置的节点，进入任意一台运行如下命令：

  ```
  var cfg ={"_id":"lagouCluster",            "protocolVersion" : 1,            "members":[                {"_id":1,"host":"192.168.211.133:37017","priority":10},                {"_id":2,"host":"192.168.211.133:37018"}            ]         }
  rs.initiate(cfg)
  rs.status()
  ```

- 节点的动态增删

  ```
  //增加节点
  rs.add("192.168.6.131:37011")
  //删除slave节点
  rs.remove("192.168.6.131:37011")
  ```

- 复制集成员的配置参数

  | 参数         | 类型   | 说明                                                         |
  | ------------ | ------ | ------------------------------------------------------------ |
  | _id          | 整数   | 复制集中的标识                                               |
  | host         | 字符串 | 节点主机名                                                   |
  | arbiterOnly  | 布尔值 | 是否为仲裁节点                                               |
  | priority     | 整数   | 权重。默认是1，是否有资源变成主节点，取值范围0-1000，0表示永远不会变成主节点 |
  | hidden       | 布尔值 | 隐藏。权重为0才可以设置                                      |
  | votes        | 整数   | 是否为投票节点。0不投票 1投票                                |
  | slaveDelay   | 整数   | 从库延迟多少秒                                               |
  | buildIndexed | 布尔值 | 主库的索引，从库也创建。_id索引无效                          |

- 仲裁节点

  rs.addArb("192.168.211.133:37020") 

##### 分片集群 

- 什么是分片

  分片是指将大型集合数据水平分割到不同的服务器上

- 为什么要分片

  单机的CPU、磁盘等有限制

  垂直扩展：增加更多的CPU和存储资源来扩展容量

  水平扩展：将数据分布在多个服务器上，水平扩展即分片

- 分片的工作原理

  ![image-20200825234804498](C:\Users\x1850\AppData\Roaming\Typora\typora-user-images\image-20200825234804498.png)

  分片集群由以下3个服务组成： Shards Server: 每个shard由一个或多个mongod进程组成，用于存储数据。 Router Server: 数据库集群的请求入口，所有请求都通过Router(mongos)进行协调，不需要在应用程 序添加一个路由选择器，Router(mongos)就是一个请求分发中心它负责把应用程序的请求转发到对应的 Shard服务器上。 Config Server: 配置服务器。存储所有数据库元信息（路由、分片）的配置

- 片键

  用于分割集合的字段

- 区块

  在shard内部，mongoDB会将数据分为chunks，每个chunk代表分片中的一部分数据（左闭右开）

- 分片策略

  - 范围分片

    ![image-20200825235933460](C:\Users\x1850\AppData\Roaming\Typora\typora-user-images\image-20200825235933460.png)

    基于分片主键的值切分数据，每一个区块将会分配到一个范围

    范围分片适合一定范围内的查找

    缺点：如果片键有明显递增/递减的趋势，则新插入的文档会分布到同一个chunk，无法扩展写的能力

  - hash分片

    ![image-20200826000314171](C:\Users\x1850\AppData\Roaming\Typora\typora-user-images\image-20200826000314171.png)

    根据片键的hash值进行切分

    优点：将文档随机分散到各个chunk，充分扩展写能力

    缺点：不能进行范围查询

#### MongoDB安全认证

##### 用户相关操作

- 添加用户

  ```
  db.createUser(  
  {    
  	user: "账号",   
      pwd: "密码",   
      roles: [     
      	{ role: "角色", db: "安全认证的数据库" },   
          { role: "角色", db: "安全认证的数据库" }    
          ]  
   } )
  
  ```

- 修改密码

  ```
   db.changeUserPassword( 'root' , 'rootNew' ); 
  ```

- 用户添加角色

  ```
   db.grantRolesToUser( '用户名' , [{ role: '角色名' , db: '数据库名'}]) 
  ```

- 验证用户

  ```
    db.auth("账号","密码") 
  ```

- 删除用户

  ```
   db.dropUser("用户名") 
  ```

##### 角色

- 数据库内置角色

  ```
  read：允许用户读取指定数据库 
  readWrite：允许用户读写指定数据库 
  dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问 system.profile 
  userAdmin：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户 clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限 readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限 readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限 userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限 dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限 
  root：只在admin数据库中可用。超级账号，超级权限 
  dbOwner：库拥有者权限，即readWrite、dbAdmin、userAdmin角色的合体
  
  ```

- 不同类型用户对应的角色

  ```
  数据库用户角色：read、readWrite 
  数据库管理角色：dbAdmin、dbOwner、userAdmin 
  集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager 
  备份恢复角色：backup、restore 
  所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、      dbAdminAnyDatabase 
  超级用户角色：root 这里还有几个角色间接或直接提供了系统超级用户的访问（dbOwner 、userAdmin、 userAdminAnyDatabase
  ```

##### 单机安全认证实现流程

测试用例：数据库test，zhangdan具有读写权限，lisi只读（注意使用的数据库）

开启安全检查之前，admin库至少有一个管理员（admin库中的用户都被视为管理员）。如果admin库没有用户的话，其他数据库即使创建了用户，启用身份验证，其默认的连接方式依然会有超级权限，任然可以不验证账号密码照样能进行CRUD，安全认证相当于无效

1. 创建管理员root

   ```
   use admin;
   
    db.createUser( { user:"root",  pwd:"123456",  roles:[{role:"root",db:"admin"}]})
   ```

2. 创建zhangsan、lisi

   ```
   use test;
    db.createUser( { user:"zhangsan",  pwd:"123456",  roles:[{role:"readWrite",db:"test"}]})
    
     db.createUser( { user:"lisi",  pwd:"123456",  roles:[{role:"read",db:"test"}]})
   
   ```

3. mongoDB安全认证方式启动

   ```
   两种方式
   1、mongod --dbpath=数据库路径   --port=端口    --auth 
   2、配置文件中加入auth=true
   ```

4. 登录验证

   ```
   use admin;
   db.auth("root","123456");
   
   use test;
   db.auth("zhangsan","123456");
   ```

##### 分片集群安全认证

1. 进入路由服务器，创建管理员和普通用户（与单机方式一样）

2. 关闭所有的配置节点、分片节点、路由节点

   ```
   //利用工具，批量关闭
   //安装psmisc
   yum install psmisc
   //使用killall
   killall mongod
   ```

3. 生成密钥文件，并修改权限

   ```
   openssl rand -base64 756 > data/mongodb/testKeyFile.file 
   chmod 600  data/mongodb/testKeyFile.file
   ```

4. 修改配置节点、分片节点的配置文件

   ```
   //开启安全验证
   auth=true 
   //指定密钥文件位置
   keyFile=data/mongodb/testKeyFile.file
   ```

5. 修改路由配置文件

   ```
   keyFile=data/mongodb/testKeyFile.file
   ```

   

6. 启动所有节点，使用路由进行权限验证（可以编写脚本，批量启动）

   ```
   ./bin/mongod  -f config/config-17017.conf
   ./bin/mongod  -f config/config-17018.conf 
   ./bin/mongod  -f config/config-17019.conf 
   ./bin/mongod  -f shard/shard1/shard1-37017.conf 
   ./bin/mongod  -f shard/shard1/shard1-37018.conf 
   ./bin/mongod  -f shard/shard1/shard1-37019.conf 
   ./bin/mongod  -f shard/shard2/shard2-47017.conf 
   ./bin/mongod  -f shard/shard2/shard2-47018.conf 
   ./bin/mongod  -f shard/shard2/shard2-47019.conf 
   ./bin/mongos  -f route/route-27017.conf
   
   ```

7. spring boot连接安全认证的分片集群

   ```
   #spring.data.mongodb.host=192.168.6.132
   #spring.data.mongodb.port=27011
   #spring.data.mongodb.database=lg_resume
   #spring.data.mongodb.username=lagou_gx
   #spring.data.mongodb.password=abc321
   spring.data.mongodb.uri=mongodb://lagou_gx:abc321@192.168.6.132:27011/lg_resume
   ```

   











