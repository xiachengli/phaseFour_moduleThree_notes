#### 图和Neo4j

##### 图和节点

图由一组节点和连接这些节点的关系组成，图形数据存储在节点和关系的属性上

使用圆表示一个节点，可以为节点添加属性。属性为键值对表示的数据

![image-20200826084017941](C:\Users\x1850\AppData\Roaming\Typora\typora-user-images\image-20200826084017941.png)



##### 图模型规则

![image-20200826084112186](C:\Users\x1850\AppData\Roaming\Typora\typora-user-images\image-20200826084112186.png)

- 图表示节点、关系和属性中的数据
- 节点和关系都包含属性
- 关系连接节点
- 属性为键值对形式
- 节点用圆圈表示，关系用箭头表示
- 关系具有方向：单向或双向
- 每个节点包含“开始节点”、“结束节点”

##### 知识图谱和图库

- 知识图谱

  由节点和边组成。其中节点即实体，由一个全局唯一的ID标识；边用于连接两个节点

- 图数据库

  图数据库指的是以图这种数据结构的形式来存储和查询数据的数据库

- 图形数据库优势

  可以利用边表示更为复杂的关系

  - 性能上，对长程关系的查询速度快
  - 擅于分析隐藏的关系。判断两个节点之间有无路径，可以发现事务间的关联

##### Neo4j基础

Neo4j是一个开源的基于java开发的图形数据库，它将结构化数据存储在图中而不是表中。它是一个嵌入式，基于磁盘，具备事务特性的java持久化引擎

Neo4j主要构建块

- 节点

  节点是图的基本单位，包含键值对的属性

- 属性

  属性是节点、关系中的数据

- 关系

  表示两个节点之间的关系

- 标签

  标签将一组节点和关系相关联

- 数据浏览器

#### Neo4j CQL

##### CQL简介

CQL为Cypher查询语言

- 是Neo4j图形数据库的查询语言
- 是一种声明性模式匹配语言
- 遵循SQL语法
- 它的语法是非常简单且人性化的格式

常用命令：

| 命令       | 作用                 |
| ---------- | -------------------- |
| create     | 创建节点，关系，属性 |
| match      | 检索数据             |
| return     | 返回查询结果         |
| where      | 条件过滤             |
| delete     | 删除节点，关系       |
| remove     | 删除属性             |
| set        | 添加或更新标签       |
| order by   | 排序                 |
| skip limit | 分页                 |
| distinct   | 去重                 |

##### CREATE

```
CREATE (   
	<node-name>:<label-name>  
    [{        
    	<property1-name>:<property1-Value>     
        ........      
        <propertyn-name>:<propertyn-Value>   
        }] )

```

| 元素                             | 说明     |
| :------------------------------- | -------- |
| <node-name>                      | 节点名称 |
| <label-name>                     | 标签名称 |
| <property-name>:<property-Value> | 属性     |

##### MATCH RETURN命令语法

```
MATCH (   
	<node-name>:<label-name> 
) 
RETURN   
	<node-name>.<property1-name>,  
    ...   
    <node-name>.<propertyn-name>
```

##### 关系创建

```
CREATE(<node1-name>)-[<relationship-name>:<relationship-label-name>]->(<node2name>)


```

##### CREATE创建多个标签

```
CREATE (<node-name>:<label-name1>:<label-name2>.....:<label-namen>) 
如: CREATE (person:Person:Beauty:Picture {cid:20,name:"hh"})
```

##### WHERE子句

```
简单的WHERE子句   
WHERE <condition> 
复杂的WHERE子句   
WHERE <condition> <boolean-operator> <condition>

例：
MATCH (person:Person) WHERE person.name = '范闲' OR person.name = '靖王世子' RETURN person
```

##### DELETE子句和REMOVE子句

```
match p = (:Person {name:"林婉儿"})-[r:Couple]-(:Person)  delete r 

MATCH (person:Person {name:"哈哈"}) REMOVE person.cid
```

##### SET子句

- 向现有节点或关系添加新属性
- 更新属性值

```
MATCH (person:Person {cid:1}) SET person.money = 3456,person.age=25
```

##### ORDER BY子句

默认升序

```
MATCH (person:Person) RETURN person.name,person.money ORDER BY person.money DESC
```

##### SKIP和LIMIT

skip跳过指定行数

limit限制返回的结果数

```
MATCH (person:Person) RETURN ID(person),person.name,person.money ORDER BY person.money DESC   skip  4  limit  2
```

##### DISTINCT去重

```
MATCH (p:Person) RETURN Distinct(p.character)
```

#### Neo4j CQL高级

##### CQL函数

- 字符串函数

  | 函数      | 功能描述       |
  | --------- | -------------- |
  | UPPER     | 将字母转为大写 |
  | LOWER     | 将字母转为小写 |
  | SUBSTRING | 截取字符串     |
  | REPLACE   | 替换字符串     |

  ```
  MATCH (p:Person) RETURN ID(p),LOWER(p.character)
  ```

- 聚合函数

  | 函数  | 功能描述     |
  | ----- | ------------ |
  | COUNT | 统计返回行数 |
  | MAX   | 最大值       |
  | MIN   | 最小值       |
  | SUM   | 求和         |
  | AVG   | 求平均值     |

  ```
  MATCH (p:Person) RETURN MAX(p.money),SUM(p.money)
  ```

- 关系函数

  | 函数      | 功能描述           |
  | --------- | ------------------ |
  | STARTNODE | 获取关系的开始节点 |
  | ENDNODE   | 获取关系的结束节点 |
  | ID        | 获取关系的ID       |
  | TYPE      | 关系的类型         |

  ```
  match p = (:Person {name:"林婉儿"})-[r:Couple]-(:Person) RETURN STARTNODE(r)
  ```

- shortestPath最短路径

  ```
  MATCH p=shortestPath( (node1)-[*]-(node2) )    RETURN length(p), nodes(p)
  ```


##### CQL多深度关系查询

- 使用with关键字

  match (na:Person)-[re]->(nb:Person) where na.name="范闲"  WITH na,re,nb match (nb:Person)[re2]->(nc:Person) return na,re,nb,re2,nc 

- 直接拼接关系节点

  match (na:Person{name:"范闲"})-[re]->(nb:Person)-[re2]->(nc:Person) return na,re,nb,re2,nc

- 使用深度运算符

  可变数量的关系->节点可以使用-[:TYPE*minHops..maxHops]

  match data=(na:Person{name:"范闲"})-[*1..2]-(nb:Person) return data

##### 事务

为了保证数据的完整性与一致性，Neo4j也支持ACID特性

```
1.所有对Neo4j数据库的数据修改操作都必须封装在事务里
2.默认的隔离级别是read_commited
3.死锁保护已经内置到核心事务管理
4.除特别说明外，Neo4j的API操作都是线程安全的
```

##### 索引

Neo4j支持节点或关系属性上的索引，以提高应用程序的性能

可以为具有相同标签名称的属性创建索引

- 创建单一索引

  ```
  CREATE INDEX ON :Label(property)
  
  CREATE INDEX ON :Person(name) 
  ```

- 创建复合索引

  ```
  CREATE INDEX ON :Person(age, gender) 
  ```

- 全文索引

  ```
  db.index.fulltext.createNodeIndex
  db.index.fulltext.createRelationshipIndex
  
  call db.index.fulltext.createNodeIndex("索引名",[Label,Label],[属性,属性])
  call db.index.fulltext.createNodeIndex("nameAndDescription",["Person"],["name", "description"])
  
  call db.index.fulltext.queryNodes("nameAndDescription", "范闲") YIELD node, score RETURN node.name, node.description, score
  ```

- 查看和删除索引

  ```
  //查看
  call db.indexes  或 :schema
  
  //删除
  DROP INDEX ON :Person(name)
  call db.index.fulltext.drop("nameAndDescription")
  
  
  ```

##### 约束

唯一性约束：

作用：避免重复记录、强制执行数据完整性规则

创建唯一性约束：CREATE CONSTRAINT ON (变量:<label_name>) ASSERT 变量.<property_name> IS UNIQUE

删除唯一性约束：DROP CONSTRAINT ON (cc:Person) ASSERT cc.name IS UNIQUE 

查看约束：call db.constraints 或:schema

#### Neo4j之Admin管理员操作

##### 数据库备份与恢复

```
社区版：冷备份（需关闭服务）
企业版：冷备份 或 热备份

//关闭服务
./bin/neo4j stop

//备份到文件
./bin/neo4j-admin dump --database=graph.db --to=/root/y.dump
//还原
./bin/neo4j-admin load --from=/root/y.dump --databse=graph.db --force

//启动服务
./bin/neo4j start
```

注意，运行数据备份可能会警告
WARNING: Max 1024 open ﬁles allowed, minimum of 40000 recommended. See the Neo4j manual

编辑/etc/security/limits.conf，在文件最后加入下面这段代码

```
*               soft     nofile          65535 
*               hard     nofile          65535
```

##### 调优思路

- 增加服务器内存和调整neo4j配置文件

  ```
  # java heap 初始值 
  dbms.memory.heap.initial_size=1g 
  # java heap 最大值，一般不要超过可用物理内存的80％ 
  dbms.memory.heap.max_size=16g 
  # pagecache大小，官方建议设为：(总内存-dbms.memory.heap.max_size)/2， dbms.memory.pagecache.size=2g
  
  ```

- 预热数据

  ```
  MATCH (n) OPTIONAL MATCH (n)-[r]->() RETURN count(n.name) + count(r);
  ```

- 查看执行计划进行索引优化

  对执行计划的生成，Neo4j使用的都是基于成本的优化器（Cost Based Optimizer，CBO），用于制订 精确的执行过程。可以采用如下两种不同的方式了解其内部的工作机制：
  EXPLAIN：是解释机制，加入该关键字的Cypher语句可以预览执行的过程但并不实际执行，所以也不 会产生任何结果

  PROFILE：则是画像机制，查询中使用该关键字，不仅能够看到执行计划的详细内容，也可以看到查询的执行结果

  需要关注的指标

  ```
    estimated rows： 需要被扫描行数的预估值    
    dbhits： 实际运行结果的命中绩效   
    两个值都是越小越好
  ```

#### Neo4j程序访问

两种访问方式

- 嵌入式数据库
- 服务器模式（通过REST访问）

嵌入式数据库

性能最佳，通过指定数据的存储路劲以编程方式访问

```
 <dependency>     
 	<groupId>org.neo4j</groupId>     
 	<artifactId>neo4j</artifactId>     
 	<version>3.5.5</version> 
 </dependency
 
 public class EmbeddedNeo4jAdd {   
 	private static final File databaseDirectory = new File( "target/graph.db" );    	public static void main(String[] args) {       
    	GraphDatabaseService graphDb = new             GraphDatabaseFactory().newEmbeddedDatabase(databaseDirectory); 
        System.out.println("Database Load!");        
        Transaction tx = graphDb.beginTx();        
        Node n1 = graphDb.createNode();        
        n1.setProperty("name", "张三");      
        n1.setProperty("character", "A");      
        n1.setProperty("gender",1);      
        n1.setProperty("money", 1101);      
        n1.addLabel(new Label() {          
        @Override            
        public String name() {                
        	return "Person";           
            }        });       
        String cql = "CREATE (p:Person{name:'李 四',character:'B',gender:1,money:21000})";        
        graphDb.execute(cql);        
        tx.success();       
        tx.close();        
        System.out.println("Database Shutdown!");        graphDb.shutdown();    } }

 
```

服务器模式

作为独立应用程序，它比嵌入式更安全且易于监控

```
<dependency>   
	<groupId>org.neo4j</groupId>    
	<artifactId>neo4j-ogm-bolt-driver</artifactId>    
	<version>3.2.10</version> 
</dependency>

public class Neo4jServerMain {    
	public static void main(String[] args) {       
    	Driver driver = GraphDatabase.driver( "bolt://127.0.0.1:7687",                AuthTokens.basic( "neo4j", "123456" ) );        
    	Session session = driver.session();
        String  cql = "MATCH (a:Person) WHERE a.money > $money " +                "RETURN a.name AS name, a.money AS money order by a.money ";        
        Result result = session.run( cql,parameters( "money", 1000 ) );        		while ( result.hasNext() )        {         
        Record record = result.next();         
        System.out.println( record.get( "name" ).asString() + " " + record.get( "money" ).asDouble() );        }        
        session.close();        
        driver.close();    }
}



```

##### SpringBoot整合Neo4j

- 引入maven依赖

  ```
  <dependencies>        
  	<dependency>            
  		<groupId>org.springframework.boot</groupId>   
          <artifactId>spring-boot-starter-data-neo4j</artifactId>   
      </dependency>       
      <dependency>            
          	<groupId>org.neo4j</groupId>            
          	<artifactId>neo4j-ogm-bolt-driver</artifactId>        
      </dependency>   
  </dependencies>
  
  ```

  

- 建立实体类

  ```
  @NodeEntity 
  public class Person {    
  	@Id    
  	@GeneratedValue    
  	private   Long  id;    
  	@Property("cid")    
  	private   int   pid;    
  	@Property   
      private   String name;     
      //构造器 setter/getter/toString
  }
   
  ```

- 数据持久化类

  ```
  @Repository 
  public interface PersonRepository   extends  Neo4jRepository<Person,Long> {  
  	@Query("match(p:Person) where p.money > {0} return p")   
  	List<Person>   personList(double money);   
  	
  	@Query("MATCH p=shortestPath((person:Person {name:{0}})-[*1..4](person2:Person {name:{1}}) ) RETURN p")   
  	List<Person>  shortestPath(String startName,String endName);
  }
  
  ```

  

- 修改配置文件

  ```
  spring:  
    data:   
      neo4j:     
        username: neo4j      
        password: 123456      
        uri: bolt://192.168.211.133:7687     
        #uri: http://192.168.211.133:7474    
        #uri: file:///target/graph.db  
  ```

  

- 编写服务类

  ```
  @Service 
  public class Neo4jPersonService {   
  	@Autowired   
      private PersonRepository  personRepository;   
      
      public List<Person> personList(){       
      	return personRepository.personList();    
      }    
  } 
  ```

  



