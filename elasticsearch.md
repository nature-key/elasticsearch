1.什么是搜索 
互联网收缩：电商搜索
系统索引
2.用数据库搜索怎么样
 首先他会全文搜索，获取所有字段
 在这，不能进行分词搜索
3.全文检索

  全文检索： 

  把数据哭的数据进行拆分成一些关键字 ，建立倒排索引，把搜索的的句子在拆分，在倒排索引中去搜索
  获取结果

  数据库是要遍历10万次扫描，每次扫描都要进行匹配，也不能进行拆分关键词

  es需要把10万数据进行拆分，成100万关键此，在倒排索引进行扫描时候匹配获取数据

 Lucene就是一个jar包，里面包含了算法和各种建立的倒排索引，我们可以依赖Lucene基础上开发，功能珍贵进行索引数据，进行搜索
 
 4什么是elaticsearch
  1.自动维护节点的索引建立，搜索请求分不到各个节点
  自动维护数据额冗余副本。保证机器人演技，不会对视任何数据
  封装更多高级功能，以及提供和高级功能的支持，快速开发， 开发能复杂的应用，复杂搜索功能，聚合分析的功能
  比如基于地理文职的搜索 
 5.es功能
  1.分布式搜索引擎和数据分析
  搜索引擎 百度搜索
  数据分析：进一个月的排名销量高的牙膏
  2.全文检索 ，结构化搜索，数据分析
  全文搜多：如查看商品名称
  结构化搜索：日用品商品有哪些
  数据分析；我们分析每一个商品分类型的数量
  3.分布式海量搜索
  分布式：将数据分不到多台机器上
  海量搜索；分布式以后，经数据处理
  近实时：达到秒级响应
  单机应用 lucene智能部署单台服务
 6es适应场景
 7.ES特点
  1，可以作为大型分布式集群。处理大量数据
  2.主要是吧数据分析和全文检索，分布式技术合并在一起，Lucene
  3.开箱即用，使用简单
  4，是对传统数据的补充，如数据分析，排名，同义词处理

 8.lucene和Es的前世今生
  Lucene 先进。功能强大，使用复杂，api逻辑繁琐
  elasticsearch 基于Lucene,隐藏复杂性，提供简单易用的api和java api
  分布式文档存储引擎
  分布式的搜索引擎，分析引擎
  分布式，处理PB技术局
  开箱即用优秀的默认参数，开源 
 9.ES的核心概念
   documeent 文档 即每一条数据
   index 索引 数据库
   type 类型 分组级表级别 
   shard 在集群中，把一个3t数据分布到三个节点。每一个节点叫 primary shard ,把数据分散到这个鞋机器上
    相当分片
     1.横向扩展，数据增加，把数据分布到其他机器上
     2.增加吞吐量和性能
   replica:如果一天机器人除了问题就可以请求replica，也叫
     1.高可用数据不丢失
     2，吞吐量和性能
10.实例演示
   健康桩孔
    GET /_cat/health?v
     epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
     1540050625 23:50:25  elasticsearch yellow          1         1      1   1    0    0        1             0                  -                 50.0%
   

    green :每个索引的primary shard 和replica shard 都是active
    yellow 每个索引的primary shard 都是active 但是部分replicashard 不是activa
    red 不是所有的索引的primary shard 都是active状态，部分索引丢失

    为什么出现处于一个yellow状态
    我们现在就是一个笔记本电脑，就启动一个es进程，相当有就是只有一个node,kibana默认建立了一个primary shard和一个replica shard

    默认配置每个index分配5个primary shard 和 5replica shrad， primary shard 和 replica shard 不在同一天机器人上目前就启动了一个node, 相当只有一个primary shard 分配启动了，里一个replica shard 没有启动 

   获取索引名
   GET /_cat/indeices?v
   health status index   uuid                   pri rep docs.count docs.deleted store.size pri.store.size
   yellow open   .kibana aEWD0mUiTZqNoEEzx6D-9w   1   1          1            0      3.1kb          3.1kb
  
11.esF搜索方式
  1.query string search 
    Get /ecommerce/product/_search

    took：消耗的毫秒
    time_out:是否超时
    _shards.total : 数据分成5片，所有请求到这个五个分片
    hits.total:查询的数量
    hits.max_score:越接近月匹配分数也高
	{
	"took": 4,
	"timed_out": false,
	"_shards": {
	"total": 5,
	"successful": 5,
	"failed": 0
	},

 2.query DSL
   GET /ecommerce/product/_search
  {
  "query": {
    
    "match_all": {}
   }
  }
  分页查询
  GET /ecommerce/product/_search
	{
	"query": {
	"match_all": {}
	}, 
	"from": 2, 
	"size": 1
	} 

	查出指定名称个字段
	GET /ecommerce/product/_search
	{
	"query": {
	"match_all": {}
	},
	"_source": ["name","price"]
	}

   过滤条件
   GET /ecommerce/product/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "test"
          }
          
        }
        
        
      ],
      "filter": {
        "range": {
          "price": {
            "gt": 3
          }
        }
      }
    }
  }
}

全文件锁
  product 这个字段进行拆分
   搜索的是  zhnag  li 
   zhnag  1234
   san    1
   li     4
   wang   3
   san    1
 短语搜索
   匹配的短语要全部包含才算匹配
   }
GET /ecommerce/product/_search
{
  "query": {
    "match_phrase": {
      "producter": "zhnag li"
    }
  }
  
}

高亮
GET /ecommerce/product/_search
{
  "query": {
    "match": {
      "producter": "zhnag li"
    }
  },
  "highlight": {
    "fields": {
       "producter": {}
    }
  }

}

11.聚合分析
  获取商品tag下的数量
  GET /ecommerce/product/_search
{
  "aggs": {
    "group_by_tag": {
      "terms": {
        "field": "tag"
        
      }
    }
  } 
}
设置映射关系
PUT ecommerce/_mapping/product
{
  "properties": {
    "tag":{
      "type": "text",
      "fielddata": true
    }
  }
  
}

获取分组后的每个组里面的价格的平均值
GET /ecommerce/product/_search
{
   "size": 0, 
   "aggs": {
     "group_by_tag": {
       "terms": {
         "field": "tag"
       },
       "aggs": {
         "avg_price": {
           "avg": {
             "field": "price"
           }
         }
       }
     }
   }
}
每个商品的分组，进行按组的平均价格排序
GET  ecommerce/product/_search
{
  "size": 0, 
  "aggs": {
    "group_by_group": {
      "terms": {
        "field": "tag",
        "order": {
          "avg_price": "asc"
        }
      },
      "aggs": {
        "avg_price": {
          "avg": {
            "field": "price"
          }
        }
      }
    }
  }
}
每个商品几个范围分组，在进行tag分组。把价格进行平均，进行升序
GET ecommerce/product/_search
{
  "size": 0, 
  "aggs": {
    "range_price": {
     "range": {
       "field": "price",
       "ranges": [
         {
           "from": 0,
           "to": 10
         },
         {
           "from": 10,
           "to": 20
         },{
           "from": 20,
           "to": 30
         }
       ]
     }
     , "aggs": {
       "group_by_tag": {
         "terms": {
           "field": "tag"
           , "order": {
             "avg_price": "asc"
           }
         },
         "aggs": {
           "avg_price": {
             "avg": {
               "field": "price"
             }
           }
           
         }
       }
     }
    }
  }  
}

12、分布式架构图

1.隐藏复杂性
  分片机制 ：我们插入数据，不用关系数据到那个shard,也不用关心这么分片
  cluster discovery 集群发现，启动一个进程，node自动发现集群，分布数据，以及replica shard
  shard 负载均衡 
  shard 副本 请求路由，集群扩展，shard从分配
2.垂直扩容和水平扩容
  垂直扩容，就是更换更好的服务器，内存更大
  水平扩容：就是添加普通机器，分散数据

  一般都是水平扩容。垂直扩容成本比较高，一般水平扩容也能达到要求

3.增加减少节点数据relalance
 保持负载均衡
   增加节点，根据服务的情况，自动从新分配shard.到新的的服务上

4.master节点
 master负责索引的建立删除，维护元数据
 默认有一天是master
 但是不是处理所有的请求，不然后会变成单点瓶定
 5.节点对等分布式架构
   节点对等，每个节点可以接受所有请求
   自动请求路由
   响应收集
13.shard 和replica机制再次梳理
    1.index可以放到多个shard ,把三个ts的数据分到三个shard上，
    2.每一个shrad是一个单元。承载数据，是一个lucene实例，建立索引和处理请求
    3.增删节点，进行自动进行负载均衡
    4.每一个doucmeent分配到一个primary shard 和replica shard 上
    5.primary shard 创建索引的时候就固定了，replica shard 数量可以随时修改
    6.primary shard 默认是5.replica默认是1，默认有10个shard ,5 个primarty shard 5个replica shard
    7.不能把把replica shard 和 primary shard 放到同一天机器上，不然起不到容错的

   单个node环境下创建index是什么样子的
    1.单nodes环境下。创建一个index  3个primary shard 和3个 replica shard 
    2.j集群的status yellow
    3.只会把3个primary shard分配不进行3个replica shard 
    4集群可以正常工作
    put /test_index
    {
      "setting":{
        "number_of_shards":3,
        "number_of_replica":1

      }

    }

14.2个节点时候如何分配replica
   当新启动一个node时候，集群自动添加，创建replica shard ，老的node是一个master，里面的primary shard
   会复制数据到对应的replica中，也可已进行读操作
15.扩容过程如何超出扩容极限，以及提高容错行   
    1.primary和replica自动负载均衡，6 shard 3个primary 3个replica
    2.每一个node有更少的shard,分配给每个shard更多的资源，每个shard性能更好
    3.扩容的极限，最多有6台机器，每一个shard使用一台机器，新能修好
    4.超出扩容极限，就动态添加replica数量，9个shard.扩容到9台服务，每一个primary shard 有2个replica shard ，拥有3被的性能
    5，6个primary shard但是不能有任何机器宕机，9个shard 最多2台机器宕机容错行更好
    6.提高容错行，以及扩容极限的方式
16.容错机制
  1.master选举，自动选举例外一个node为master
  2.node宕机的时刻，po这个primary shard 没有了西式就不是 actie status ,就不是所有的primary shard都是active
  3,新的master将丢失的primary shard的谋个replicn shard
  提升为primary shard 此时cluster status 会变成yellow
  所有的primary shard 变成了active ，所以不是所有的replica shard dsoushi active

  4.重启故障node ,新的master 会将失去的副本都复制一份到
  之前的shard 数据，同步一下修改，status变成green    
17._index，_type,_id
_index:代表document在呢个index中，名称必须小写，包含了类似的document
_type：一个大的index中的分类，名称小写
_id：唯一标识

18.自动生成id和手懂生成id
  手动生成id
   put /index/type/2
  当冲数据库导入的时候就可以使用手动生成
  自动生成
  post /index/type
  {
    "content":"test"
  }
  自动生成id 使用GUID生成id。不会发生冲突，20个字符
19.自定义——souce返回结果解析

  返回在json 的字段
  GET /ecommerce/product/1?_source=price  
 20. 全量替换。 
   1.全量替换
     当使用put q请求时候回全量替换，但是之前的那条数据不会删除，只不过是标记删除，当es内存不足的时候，就会把他删除，下次请求直接回去到新的document 
   2.强制创建
     可以使用
     put /index/type/4/-create
   3.DELETE test_index/test_type/1
     和全量替换删除一样，并不是真正的删除只不过进行标记

21.解决并发冲突
  悲观锁：
  1.线程A去读取库存获取100件
  2.然后这条数据加上所
  3.当线程B获取的时候，获取不到，一直等待
  4.等更新完了之后，线程B获取导数据，进行更新数据
  总结，就是一条进行所，一个线程只能操作一个数据，
  使用透明，直接枷锁，不需要额外的操作，但是，性能不高

  乐观锁：es默认
  1.当线程A去获取数据
  2，线程B也去获取数据
  3，当线程A更新完数据，ES的数据判断是否version是否相等
  4.相等就更新
  5，线程B也是一样的更新，但是查看version不一样，就不重新获取version，在进行更新，version加一
  并发执行性能高，但是繁琐，可能要重试几天，么一次都要对比vestion
22.version版本控制
  ES有乐观锁控制
   1.先生成一条数据，version =1
   2.线程A进行先修改，线程B后修改
   3，但是同步数据是异步多线程
   4，当后修改的数据线更新的时候，version成了2
   5，但是数据已经你是后修改的数据
   6，这个时候，乐观锁，会丢掉先修改的数据
   7.这样数据就变得正确了，

23，上机实现乐观锁

  j基于自己的vestion 实现乐观锁模拟
    不用es自己内部的vestion
    ?vestion=1唯一区别就是当更新的时候，版本必须一致
   ？verstion=1&verstion_type=external 额外的版本必须大于-varstionde的时候进行修改

24.partial update
   put index/type/id 更新和替换的语法一样
   使用
   post index/tyrpe/id/_update
   {
 “doc”:{

}

   }  

 本质上是一样的，但是，parual update可以减少网络请求，以及介绍冲突

 不需要吧所有的字段进行修改传过去


25.partial update

 1.拿到document 和version=1
 2.使用传过来的filed,更新document
 3.但是已经有更新过了此时version=2
 4,此时再去更新的时候，就会失败

 retry策略 retry_on_conflict
   1.再次获取document数据和最新version
   2.基于最新版本再去更新，如果成功就好
   3.如果失败，重复1，2步周，
   4，最多重试参数的值


26.批量查询

就是一条条查询，比如要查询100条案护具，need发送100此网络请求
，开销大，如果批量处理，就只发送一次网络请求，网络请求
缩减100陪
 1.mget批量
GET /_mget
{
  "docs":[
    {
      "_index":"test_index",
      "_type":"test_type",
      "_id":1
    },
    {
      "_index":"test_index",
      "_type":"test_type",
      "_id":10
    }
    
    ]
}
2同一个index下的type
GET test_index/_mget
{
  "docs":[
     {
       "_type":"test_type",
       "_id":10
     },
     {
       "_type":"test_type",
       "_id":1
     }
    
    ] 
  
}
3.同一个index,type下的数据

GET /test_index/test_type/_mget
{
  "ids":[1,10]
}
4.尽可能使用mget处理，减少网络请求

26，biuk批量增删改查

 1.delete删除一个文档。只要一个json就行
 2.create 强制创建
 3.index 普通put操作，可以创建文档，也可以全量替换
 4，update执行partial update操作

 一行一个json
任意一个失败，不会影响其他操作

27.可以开发什么类型的应用程序
1.数据量大，es分布式本质，可以帮助快速扩容，承载数据
2.数据解雇灵活多变，随时可能变化，而且数据结构负责
3.对数据的相关操作，较为简单，比如增删改查
4.nosql,数据库，使用的也是类似上面额这种场景



28.dcocument数据的路由原理
1.document路由到shard上是什么意思
  一个index会有多个shard，每一个doucment都会到一个shard上
 2.路由算法
  shard =hash(rounting)%number_of_primarty_shard
number_of_primarty_shard :shard的个数
rounting:m默认代表id .可以搜东指定

 当获取一个id值，计算hash的值，相同的rounting值每次过来hash值都是一样
 ，求余的值=shard数目-1.就是shard范围内

 3._id  or  custom rounting value

  默认rounting是id
  也可以在手动指定 index/type/id?rounting=userid
  可以保证doucment一定路由到一个shard中，在后续应用级别上负载均衡
  以便批量操作提高性能

  4.primary shard 不可变谜底

    疑问当第一个数据放到了衣蛾shard1中
    在获取的时候，路由的shard就不正确了，导致数据丢失

29.增删改原理

   1.java客户端，发送请求
   2，到结果node节点，此时就变成协调节点，
   3.路由计算shard,协调节点路由到primary shard
   4,同步到replica shard 
   5,处理万后，协调节点响应客户端
30一致性原理
   
 1.consistency 
    one :要求我们这个写操作，只有一个primary shard s是active活跃可用，就是可以执行
    all :要求我们写操作，必须多有的shard都是活跃的
    quorum:默认要求，所有的shard必须是大部分shard都是活跃才可用
 2.quroum机制
   quroum = (primary+replica)/2+1 只有当replica>1的时候起作用
 3.如果节点数杀意quorum数量，可能导致quorum不齐全，导致不能写操作
   当1 primary shard replica=3.quroum=(1+3)/2+1=3
   只有2各节点，所以就不能进行写操作
 4timeout
  如果不齐全，就默认等1mins,put index/type/id?timeout=30  


 31.document查询的原理 
  1java应用请求一个节点，这个节点就是协调节点
  2.协调节点路由之后就知道在哪个shard,
  3,不在是仅仅在primary shard 上
  4.采取随机的算法，4次查询有两次在primary
    另外2此就在replica shard 上
   5.特殊情况当index正在建立的时候，replica还没有的时候，由于到RO就会返回找不到
     doucument 建立完成之后，就有了数据 
32，bulk 奇特json
   {"action":{"mete"}}
   {"data"}
   {"action":{"mete"}}
   {"data"}

   1.如果按照通俗易懂的格式
   2.他显示转化成jsonArray对象，这个时候就会出现一份考本
   3.解析json数组的对象，对每个document进行路由
   4.请求到对应shard上
   5.将个请求序列化
   6.将序列化的请求发送到对应的节点上


   这个回到导致内存增加，引起jvm垃回抽，影响性能


   1. 不用转化成json对象，不会出现内存相同的数据考本，直接换行其人格
   2.每两个一组，读取mete,进行document路由
   3，直接将对应的json发送到node上

   最大优势在于，不要讲json数组不解析成一个jsonArray，不消耗内存，节省消耗
33.timeout解密
   timeout机制，指定每个shard,就只能在这个时间范围内，将搜多的数据
   范湖给client,而不是等待查出所有的数据，确保，一次收缩可以在用户指定范围内完成
   ，提供良好的支持
  get /_search?timeout=10ms   



34,mult_index mult_type搜索模式

  /_search 所有索引和所有type 的数据搜索
  /index/_search
  /index*/_search
  /index1,index2/_sear
  /index/type/_search
  /index1.index2/type1/type2/_search
  /_all/type1,type2/_search

   请求会把回到所有的primaary shard ,因为，每一个primary shard
   都会有数据，如果有replica shard ，请求也回到replica shard 中


35.分页和deep paging

 get /_search?from=0&size=10

 deep paging 就是深度搜索
  比如，单个主节点，每个节点都有2万数据，当搜索低10000的数据的10 条时候
  。就是把三个shard de 数据到10010 条数，把三个节点数据排序
  ，公3万30，然后在协调节点，获取其中需要的十条数据

   性能影响，协调节点，要进行大量数据排序，所有这个过程，消耗网络
   宽带，消耗内存，cpu,所以尽量较少deep  paging 搜索

36 query string 基础语法
  GET /INDEX/type/_search?q=file:test
  get /index/type/_search?q=+file:test 查询file带 有test的document
  get /index/type_search?q=-file:test  查询file不带test的document

  2.all
   get /index/type/_search?q=test

   直接可以搜索所有的filed,任意一个filed包含的关键字都可一搜索踹，我们进行搜索并不是对每一个documnet
   的没一个files都进行一次搜索，
   在es的_all员数据，建立索引的时候，我们插入一条documment,他里面包含多个filed
   ，此时会自动把多个filed的值，全部用字符串串联起来，变成一个字符串，同时建立索引

   在后面如果自爱搜索的时候，没有对某个filed指定搜索，就默认默认搜索——all filed
   其中包含了所有filed值 
   
37.mapping
   get /index/type/1
   {
    "const"

   }
    在建立index的同时自动建立mapping
    由于mapping的作用，设置了mapping，导致字段的行为不一样，搜索结果不一样


39.精确匹配 ，全文搜索

   1.精确匹配
    exact value  搜索时候。必须输入2017-09-09才能搜搜出来

    2.full text
     缩写 vs    CHAIN CN
     格式化  like likes likes
     大小写  tom  TOM
     同义词  like  love

就不是简单的匹配一个值，而是可以对值进行拆分词语后进行匹配

40  倒排索引

     当建立index的时候，建立倒排索引，搜不到建立normalization
     进行在此建立倒排索引，把同义词 相似此，进行转换
41.分析器
 1.什么是分词器
   切分词语  normalization(提升recall 召回率)
   给一个句子，显示把句子进行分词，然后对每个单词进行normalization
   时代转换，单辅助替换

   chararcher filter :在一段文本分词之前，进行预处理，比如去除html标签
   tokenizer 分词 hello you and me  ==  hello,you ,and ,me
   token filter :同义词转换等等


 2，内置分词器


    standard analyzer ：大小写 单词进行分析
    simple analyaer  ：特殊字符分子_分词
    whitespzce analyzer 不进行大小写转换
    language analyzer 特定语言分词器

42.解密分词 string query

  query string 必须建立相同的analyzer进行分词
  query string 对exact value 和full text区别对待
  date : exact value 
  _all: full text

 比如document其中的filed包含value 是 hello  and  mes
 建立倒排索引。我们要搜索这个doument对应的index,搜索文本是hello me
 搜索文本就是 query string

 2.maping 
   get  _search/?q=2017
   get _search/?q=2017-01-01
   使用的——all 全文检索

    所有的字段汉字，进行分析，当使用query string进行搜索的时候
    也进行分词，对应的 2017 01 01 在三个文档都有，所以就会搜索到3个

  get /_search？q=date:2017-01-01
   由于进行搜索的时候，date是exact value ,所以是精确匹配，没有找到
   就只有一条会显示


43.什么是mapping 
  1.向es插入数据，建立index,同时建立mapping
  2.建立mapping自动建立每个field的数据类型
  3。不通的数据类型可能有extext filed 和 full  filed
  4.当时exact value 建立倒排索引的时候，把整个关键字建立
    full text 建立就是分析 normailzation （时代转换等等呢个）
  5.同时，不通的类型，搜索行为就不一样
  6.可以用es的dynamic mapping ,让其建立mappIng ,包括自动设置数据类型
  也可以手动建立mapping,

  每一个 index 的type都会有一个 mapping，建立对应数据类型


44.mapping建立

  PUT /website/_mapping/article
{
  "properties": {
    "author_id":{
            "type":"long"
          },
          "title":{
            "type":"text",
            "analyzer": "english"
          },
          "context":{
            "type":"text"
          },
          "post_date":{
            "type": "date"
          },
          "publish_id":{
            "type": "text",
            "index": "not_analyzed"
          }
    
  }
  
}

获取mapping
  
GET /website/_mapping/article



测试分析
  GET /website/_analyze
{
  "field": "new field",
  "text": "my dog"
}




46。mapping复杂数据类型以及object类型数据底层结构大揭秘

  对于object底层数据结构

PUT /company/empploy/1
{
  "address":{
     "country":"chain",
     "provice":"shenzhen",
     "city":"guodong"
  },
  "name":"wang",
  "job":"java",
  "phone":12345678900
  
  
}
GET /company/_mapping/empploy

{
  "company": {
    "mappings": {
      "empploy": {
        "properties": {
          "address": {
            "properties": {
              "city": {
                "type": "text",
                "fields": {
                  "keyword": {
                    "type": "keyword",
                    "ignore_above": 256
                  }
                }
              },
              "country": {
                "type": "text",
                "fields": {
                  "keyword": {
                    "type": "keyword",
                    "ignore_above": 256
                  }
                }
              },
              "provice": {
                "type": "text",
                "fields": {
                  "keyword": {
                    "type": "keyword",
                    "ignore_above": 256
                  }
                }
              }
            }
          },
          "job": {
            "type": "text",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          },
          "name": {
            "type": "text",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          },
          "phone": {
            "type": "long"
          }
        }
      }
    }
  }
}
底层数据结构
  address.country [chain]
  adress.provice [shenzhen]
  adress.city [guodong]
  name  [wang]

47.search api 基本语法

   Query DSL搜索语法
    GET /website/test/_search
{
  "query": {
    "bool": {
      "must": [
        { 
          "match": {
           "name":"elasticsearch"
          }
        }
      ],
      "should": [
        {
           "match": {
              "content":"dog"
           }
          
        }
      ],
      "must_not": [
        { 
           "match": {
             "id": 100
           }          
        }
      ]
    }
  }
  
  
}


GET /company/employee/_search
{
  "query": {
    "bool": {
       "must": [
         {
           "match": {
             "date": "2019"
           }
         }
       ],
       "filter": {
         "range": {
           "age": {
             "gte": 20,
             "lte": 200
           }
         }
       }
      
    }
    
  }
  
}

filter :仅仅按照搜索条件过滤需要的数据而已，不计算相关度分数
query:回去计算document相对于搜索条件的相关度，并按照相关度排序

要是想返回相似度高的先返回就是用query,仅仅是进行数据过滤使用fileter

3.性能
 filter 不需要计算相关度分数，不需要按照相关度分数进行排序，同时内置自动
 cache结果。性能高
 query：要计算相关度分数计算，按照分数进行排序，为无法cache结果

50.各种功能query实战

GET /company/employee/_search
{
  "query": {
    "term": {
      "name": "wang san"
    }
  }
  
}




GET /company/employee/_search
{
  "query":{
     "range": {
       "age": {
         "gte": 10,
         "lte": 20
       }
     }
    
  }
  
}




GET /company/employee/_search
{
  "query":{
      "multi_match": {
        "query": "wang",
        "fields": ["name","city"]
      }
    
  }
}


 term 精确匹配，不进行分词


51.组合过滤
 单独还是用filetr
  GET /company/employee/_search
{
  "query": {
    "constant_score": {
      "filter": {
         "range": {
           "age": {
             "gte": 10,
             "lte": 20
           }
         }
      },
      "boost": 1.2
    }
  }
  
}

分数计算是在bool下分别计算分数，组合后在进行计算得出

检验合法性
GET /test_index/test_type/_validate/query?explain
{
  "query":{
    "match": {
      "content": "TEXT"
    }
  }
}

排序
GET /company/employee/_search
{
  "query": {
    "match": {
      "name": "wang"
    }
  }
  , "sort": [
    {
      "age": {
        "order": "asc"
      }
    }
  ]
}
建立mapping
PUT /website
{
  "mappings": {
     "article":{
       "properties": {
         "title":{
           "type": "text",
           "fields": {
             "raws":{
               "type": "string",
               "index": "not_analyzed" 
             }
           },
           "fielddata": true
         },
         "content":{
           "type": "text"
         },
         "post_date":{
           "type": "date"
         },
         "author_id":{
           "type": "long"
         }
         
       }
     }
  }
}

title一分词，第二排序。用来titl分词排序，不准确


55.分数的计算
书案发介绍
 1.term frequency 
   搜索文本出现的filed次数越多，相关度就越高
   hello word 
   doc1 :hello you. and word 高
   doc2:  hello you  
 2.inverse document frequency
  搜索文本的各个词条出现在所有的出现的次数越多，相关度，不高
  hello word
  doc1:hello you today is good  
  doc2: how are you ,world      高
  hello字所有的发文档初心了10000此。wordle出现了100，
3.field length norm filed长度 ，越长，相关度就地

   hello word
   doc1  {"title":"hello you "}
   doc2 {"title:"you","content"："ddddd  dddddd  word"}
  所以 doc1相关度 高，英文title 长度地
查看分数的计算
GET /website/article/_search?explain
{
  "query":{
    "match": {
      "content": "elasticsearch"
    }
  }
}


56。doc value
 搜索的时候，要依靠倒排索引，排序的时候，正派索引，
 看到诶个document 的每个filed，然后进行排序就是正排索引，doc value

 doc values 是被保存在磁盘上的，此时如果内存足够，os.会自动将缓存在内存中
 性能很好，如果内存不足够，os会将其写入磁盘上


57.query prase
 1.一个请求到所有的shad
 2.每一个shard返回 from +size的proority queue,
 3。返回给coordinate，然后进行merge,全局排序后，放到自己的自己的queue
 4.此时queue,就可以将自己的priority queue中的数据，取出获取第一页数据
 5，priority queue
 这个和deep paging问题一样


replica shard提高性能， 多个 replica shard 减轻 primary shard 的压力，正价qbs 


58.fetch phase

  queryphase结束，获取到的是id, 此时九华发送
  mget api 。去各个shard获取想要的数据
  。每个shard 返回数据到coorfnate node
  ，最后返回给client

 默认 size =10

59.bounding result
 1.preference
  决定哪些shard会被来执行搜索操作
   ——primary _primary_first _local


   问题：两个document 排序。field值相同
   不通的shard 可能拍讯就是不通，每次伦旭达到不通的replica shard 
   每次页面的上看到的数据结果可能都不一样
   ，这就是 bouncing result 跳跃结果
   解决：将preference设置一个字符串
   比如user_id,让每个user每次搜索的时候，都使用同一个
   replica shard,就不会看到跳跃 

2.timeout获取指定时间数据得到返回
3.rounting ,路由到同一个 shard

4.search_type
 默认 query_then_fetch
 dfs_query_then_fetch提高revlance sort 精准度


60.scroll
 用来检索数据，让系统进行处理大量数据
  比如一次性查出来10万数据，性能很差
  此时会采取scoll滚动查询，一次一次的查
  知道数据查询完成
  scoll每次查询就会保存一个试图快照，之后
  基于就得试图快照数据收缩，如果这个期间数据更新，shi
  是不会用户看到，基于-doc排序方式，性能较高
  每次发送scroll 请求，指定一个时间窗口，
  每次搜索请求在这个时间窗口完成就可以




GET _search?scroll=1m
{
  "query": {
    "match_all": {}
  },
  "sort": [
    
      "_doc"
    
  ],
  "size":10
}

61.索引
  1.创建索引
  PUT /my_index
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  },
  "mappings": {
    "my_type":{
      "properties": {
        "my_field":{
          "type":"text"
        }
      }
    }
    
  }
}
2.修改索引
PUT /my_index/_settings
{
  "number_of_replicas": 1
  
}
3.删除搜因
DELETE /my_index
DELETE /my_index1,my_index2
DELETE /my_*
DELETE /_all

elasticsearh.yml
action.destructive_requires_name:true// 不能使用 /_all

62 自定义分词器
PUT /my_index
{
  "settings": {
    "analysis": {
      "char_filter": {
        "&_to_and":{
          "type":"mapping",
          "mappings":["&=>and"]
        }
      },
      "filter": {
        "my_stopword":{
          "type":"stop",
           "stopwords":["the","a"]
        }
      }
      , "analyzer": {
        "my_analyzer":{
          "type":"custom",
          "char_filter":["html_strip","&_to_and"],
          "tokenizer":"standard",
          "filter":["lowercase","my_stopword"]
        }
      }
    }
  }
}

GET /my_index/_analyze
{
  "text": "i&me is a DOG"
  , "analyzer": "my_analyzer"
}



定义type 的字段的分词器
PUT /my_index/_mapping/my_type
{
  "properties": {
    "title":{
      "type":"string",
      "analyzer": "my_analyzer"
    }
  }
}

63.索引管理 type底层数据结构
   type底层结构，是 相同的字段都是保存一份，不同子弹为空
   最佳实践。类似结构的type放到一个index下，这个type应该
   有多个filed是相同的，假如。你将两个type的field完全不同
   放到一个index下，那么就没一条数据会至少一版底层lucene是空值
   严重的性能问题，


64 root object
 就是mapping对应的mapping json
  包括 properties all source metadata

 put /my_index
 {
  "mapping":{
    “my_type":{
      "properties":{

      }
    }
}
 } 

  2.properties
  type index analyzer
   put /my-index/_mapping/my_type
   {
         "properties":{
          "title":{
            "type":"text"
          }
         }
   }
  3._source
   1.查询的时候，直接可以拿到完整的的document .不需要先拿document idz
   在发一次请求
   2.partial update 基于_souce
   3.reindex .直接基于——souce,不需要冲数据库，查询数据在司改
   4.可以基于_source订制返回——source
   5.debug query 更容易。可以卡到——source

   4._all
      建立索引的时候，将所有的field的值平成一个字符串，在没有指定
      field进行搜索的时候，就是使用——all field
      put /my_index/_mapping/my_tpe
      {
        "_all":{"enabled":false}
      }
    也可以在field上设置是否包含field的值在all field
    put /idnex/_mapping/type
    {
      "properties":{
        "my_field":{
          "type":"text",
          "include_in_all":false
        }
      }
    }

65.自定义 mapping
   订制dynamic策略
   true遇到陌生字段，进行 dynamic mapping
   false 遇到陌生字段 就忽略
   strict 遇到陌生字段 就报错
put /index
{
  "mapping":{
   "type":{
     "dynamic":"strict"
     "title":{
     "type":"text" 
       },
       "adress":{
       "tyep":"text",
       "synamic":true
 }
}
}
  date_detection ：自动区别字段的类型，如果是日期格式，就映射成
  date。在此插入不是date格式报错
 put inde/-mapping/type
 {
  "date-detection":false
 }

2.订制自己的dynamic mapping temloate(tyep level)
  put /index/
  {
    "mapping":{
     "type":{
        "dynamic_temlates":[
          {
          “en”:{
            "match":"*_en"
             match_mapping_type:"string",
             "mapping":{
              "type":"string",
              "analyzer":"english"(会把停用词去掉)
           }
        }

        ]
   }
  }
  }
title没有匹配到dymic就是用standard 不会过滤停用词



3.订制自动机的 default mapping template(index level)

put /index
{
   “mappings”:{

     "-default":{
       "_all":{"enable":false}
     }.
     "blog":{
        _all：{enable:true}
     }
 }
}
可以在自己对应的type可以打开

66.在不停机的情况重建reindex
     一个filed的设置是不能被修改的。。如果要修改一个field,就要会从新建立
     index,使用别名，然后bluk api 查出来，在写入新的index中
     批量查询的建议使用 scroll api  。并采用多线程的来reindex数据
     1.开始建立dynamic mapping 插入数据前几条数据都是日期
     单是后面的是字符串，就会报错
     2.这时候就要重建索引
     3.先要建立index的别名
       
      PUT /my_index/_alias/good_index
     4.新建index
     PUT /my_index_new
{
  "mappings": {
     "my_type":{
       "properties": {
         "title":{
           "type": "text"
         }
       }
     }
     
  }
   5.查询数据
   GET /my_index/_search?scroll=1m
{
  "query": {
    "match_all": {}
  },
  "sort": [
    "_doc"
  ],
  "size": 1
}
6导入数据
PUT /_bulk
{"index":{"_index":"my_index_new","_type":"my_type","_id":"2"}}
{"title":"2018-09-09"}

  7.重复执行
   8.删除旧的index
    POST /_aliases
{
  "actions": [
    {"remove": {"index": "my_index","alias": "good_index"}}, 
     { "add": {"index": "my_index_new","alias": "good_index"}}
    
  ]
}   
9.切换别名
POST /_aliases
{
  "actions": [
    {"remove": {"index": "my_index","alias": "good_index"}}, 
     { "add": {"index": "my_index_new","alias": "good_index"}}
    
  ]
}

67.索引不可变原因
  倒排索引结构
   1.包含这个关键词的 documnet list
   2. 包含关键词的所有的document 数量 IDF
   3,关键词在这个document中的次序 TF
   4，关键词在每个document中的次数
   5，每个doucment的长度
   6，关键此在所有的documen的平均长度

1.不需要锁。提高并发能力避免锁的问题i
2，数据不变，一直保存在os  cache 中
3，filter cache 一直驻留内存
4.可以压缩节省cpu 和io开销

每次都要重新构建整个索引


68.document写入原理
 1.一个doc先被写入内存 buffer中
 2.写到一定程度，就会commit
 3.先提交到 os  cache
 4.然后 fsync强制刷新cache到硬盘 disk ，index segment就会被打开公search使用
 5.同时清空 buffer

 es底层用的是lucene,lucene底层的index是分为多个segment
 每个segment都会存放到部分数据

  document 删除的时候就会生成一个.del文件，里面记录了那个index
  的那个document被删除了。下次搜索的时候，就会看到已经被删除
  就会过滤不被搜索

document 被更新的时候，也会记录在。del中，在搜索时的时候，会得到多个版本，你的
的数据，只会获取没有标记的数据，最新的版本
69.优化写入流程
   现有的的流程是，必须等待fsync将segment刷新磁盘，才将
   segment打开公search使用，这样的话，从一个documetn写入
   他可以搜索，可能超过分钟，就不是近视是了

   修改流程
    1.数据写入buffer
    2.每隔一段时间buffer数据写入segment文件，d但是写入os cache
    3.只要segment写入os  cache ,那就直接打开search使用立刻执行commit

    数据写入os cache并打开搜索过程，叫做refresh,默认
    每隔一秒refresh一次，也就是说每个一秒就把bufer的数据写入的
    index segment的数据先写入到cache中

    可以手动执行
    post /my_index/_refresh 
    可以修改时间
    put  /my_index
    {
      "setting":
      {
        "refresh_interval":"30s"
      }
    }

70.最终的document写入优化
1.数据写入buffer,每个一秒就会把数据写到os cache ,同时打开index segment，同时清空 buffer, 有一个translog记录document
2.下一此也是一样增加index segment ，继续添加到translog中
3.当translog导到一定程度，就会触发 flush,把buffer的数据写入到os cache中同时清空 buffer,
4.此时会在磁盘写入一个 commit point 记录所有的index segment
5.触发fasync把os cache的数据清醒写入os disk 
6.同时清空tanslog日志

数据恢复
 1.当宕机的时候，
 2.os  cache 数据将全部丢失，
 3.而 os disk 中记录上衣commit point 的数据
 4.translog记录了上一次到现在的数据
 5.机器重启的时候，disk的数据没有丢失，此时将tanslog文件
 变更记录进行回收，重新执行之前的各种操作。在bufer执行
 重新刷新dengment 到 os  cache,等待下一次从commit发生即可

fsync 清空translog就是flus
默认30分值给你flush一次，或者当tansLog过大时候也会flush
post /index/-flush 

translog 每个5秒回fsync到磁盘上，在一次增删改查后
当faync在primaary shard和replica shard成功之后
镇删改查才会成功
但是这种一次增删改查，强行 fsync translog 可能就会导致
比较耗时，也可以允许部分数据丢失，这是异步
put /index/_setting
{
  "index.translog.durability":"async"
  "index.tanslog.aync_interval":"5s"

}

71.sengment merge
 1.选择一些相同的segment,merge成一个segment
 2。将新的segment flush 到磁盘上
 3.写一个新的commit point 包括segment并且删除旧的segment
 4,将新的segment打开搜索
 5.删除旧的segment
 可以手动执行
 post /index/_optimize?max-num_segments=1


 1.term filter实战

  1.字符串默认创建mapping是两个字段
    一个是进行分析用的 type:string
    另一个是不进行分词 的 keyword 保留256个字符最多，直接一个字符串放入倒排索引中
  2.  term filter 天然支持 boolean  s数字  日志
  3.text 不进行分词 no_analyzed才能进行使用

 GET /forum/article/_search
{
  "query":{
    "constant_score": {
      "filter": {
        "term": {
            "articleID" : "QQPX-R-3956-#aD8"
        } 
      }
    }
  }
  
}
2.filter执行原理深度解析
    1.在倒排索引中搜索，获取cocument list
      word   doc1 doc2  doc3
      hello   *   * 
      word        *
      str= "word hello"
      找到 doc2
    2.把搜索的结果进行构建bigset
      把filter找到的结构，构建一个bigset ,一个二进值，【1，0，1，0，0】
      匹配就是1，不匹就是0
    3.遍历所有的filter的bigset,获取都满足的bigset,返回给客户端，先把稀疏的进行遍历，尽可能多的过滤数据
    
    4，把匹配额数据进行缓存，caching,把近256个query超过一定数量的条件进行缓存，晓得segment不畸形缓存
     query比filter的好处就是进行caching,性能提高
    5.filter的执行是在query,先尽量过滤大量数据
    filter:简单数据过滤，不进行相关度计算 
    query:，进行相关度计算 还score进行排序
    6.caching自动更新 缓存
    7.以后相同的filter就会在caching进行搜索 

3多个filter组合
GET /forum/article/_search
{
  "query": {
    "constant_score": {
      "filter": {
         "bool": {
           "should":[
               {
                 "term":{"articleID": "XHDK-A-1293-#fJ3"}
               },
               {
                 "bool":{
                   "must":[{
                     "term":{"postDate":"2017-01-01"}
                     
                   },
                   {
                     "term":{"articleID":"JODL-X-1937-#pV7"}
                   }
                   ]
                 }
               }
             
             ]
         }
        
      }
    }
    
  }
  
}
5.terms  相当于in
GET /forum/article/_search
{
  "query": {
    "constant_score": {
      "filter": {
        "terms": {
          "tag": [
            "java",
            "VALUE2"
          ]
        }
      }
    }
  }
  
  
}

6 范围查找
GET /forum/article/_search
{
  "query": {
    "constant_score": {
      "filter": {
        "range": {
          "view_cnt": {
            "gte": 10,
            "lte": 240
          }
        }
      }
    }
    
  }
  
}

7.控制全文检索

 GET /forum/article/_search
{
  "query": {
    "match": {
      "title": "java elasticsearch"
    }
  }
  
}
GET /forum/article/_search
{
  "query": {
    "match": {
      "title":{
        "query": "java elasticsearch"
        , "operator": "and"
      }
    }
  }
  
  
}

GET /forum/article/_search
{
  "query": {
    "match": {
      "title": {
        "query": "java elasticsearch spark hadoop",
         "minimum_should_match": "75%"
      }
    }
    
  }
  
  
}
bool多个组合搜索的分组
must和should搜索对应的分数，加起来，除以must和should的总数

排名第一：java，同时包含should中所有的关键字，hadoop，elasticsearch
排名第二：java，同时包含should中的elasticsearch
排名第三：java，不包含should中的任何关键字

should是可以影响相关度分数的

must是确保说，谁必须有这个关键字，同时会根据这个must的条件去计算出document对这个搜索条件的relevance score
在满足must的基础之上，should中的条件，不匹配也可以，但是如果匹配的更多，那么document的relevance score就会更高


GET /forum/article/_search
{
  "query": {
    "bool": {
      "must":     { "match": { "title": "java" }},
      "must_not": { "match": { "title": "spark"  }},
      "should": [
                  { "match": { "title": "hadoop" }},
                  { "match": { "title": "elasticsearch"   }}
      ]
    }
  }
}

默认情况下，should是可以不匹配任何一个的，比如上面的搜索中，this is java blog，就不匹配任何一个should条件
但是有个例外的情况，如果没有must的话，那么should中必须至少匹配一个才可以
比如下面的搜索，should中有4个条件，默认情况下，只要满足其中一个条件，就可以匹配作为结果返回

但是可以精准控制，should的4个条件中，至少匹配几个才能作为结果返回GET /forum/article/_search
{
  "query": {
    "bool": {
      "should": [
        { "match": { "title": "java" }},
        { "match": { "title": "elasticsearch"   }},
        { "match": { "title": "hadoop"   }},
  { "match": { "title": "spark"   }}
      ],
      "minimum_should_match": 3 
    }
  }
}


全文检索多个值搜索，两种做法 match query 或 bool should
控制精准度 and operator  ,minimum_should_match












