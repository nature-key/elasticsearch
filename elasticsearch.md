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



8.match 底层实现

 ”match“:{
    "title":" java elasearch"
}

"bool":{
    "should":[
     { "term":{"title":"java"} }
     {"term":{"tltle"：”elaserasetch“}}
    ] 
}

match 底层使用  bool  term 

9.权重 boost

权重大分，分数高，优先返回
GET /forum/article/_search 
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "title": "blog"
          }
        }
      ],
      "should": [
        {
          "match": {
            "title": {
              "query": "java",
              "boost": 2
            }
          }
        },
        {
          "match": {
            "title": {
              "query": "hadoop"
            }
          }
        },
        {
          "match": {
            "title": {
              "query": "elasticsearch"
            }
          }
        },
        {
          "match": {
            "title": {
              "query": "spark"
              
            }
          }
        }
      ]
    }
  }
}

10多shard搜索结果不准确
  当有多个shard的时候，其中的一个shard包含了许多关键词，根据idf算法，相关分数反而di,而另一个shard的document的关键词出现的少，这样，相关度就会高，最后搜索的结果可能就不太准确。
   
   解决
     1.生产环境下，数据量大，尽可能均匀分配
     2.测试环境将primary shard设置一个
     3.测试和环境搜索带search-type=dfs_query_rhen-fetch参数，会将local IDF计算书来 global IDF
     计算一个doc相关分数的时候后，将所有的shrad 的 local IDF计算一下，获取出来，在本地进行globa IDF分数计算，将所有的shard的doc作为上下文尽心搜索，生产环境不推荐，性能很差

11.多字段查询 dis_max

GET forum/article/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "match": {
            "title": "java solution"
          }
        }
        ,
        {
                    "match": {
            "content": "java solution"
          }
        }
      ]
    }
  }
}


分数计算
  每个query分数之和 。乘以query匹配数  除以 query数目（2）


要精准分会使用 dis_max
尽可能一个字段匹配分数高的，而不是，每个字段都匹配的分数
 计算每个 match query 分数的坐高值，获取分数高的返回
GET forum/article/_search
{
  "query": {
    "dis_max": {
     
      "queries": [
         {
           "match": {
             "title": "java solution"
           }
         },
         {
           "match": {
             "content": "java solution"
           }
         }
        ]
    }
  }
}

bis_max只会把query中最高分数拿出来
tie_breaker （值是0-1）就是把其他 query的分数，乘以tie_breaker,然后综合和最高分数一起计算，获取最高分数

还会考虑其他query分数

13.multi_match
可以吧bis_max简单些成muti_match
GET forum/article/_search
{
  "query": {
    "multi_match": {
      "query": "java solution",
      "fields": ["title^2","content"],
      "tie_breaker": 0.3,
      "minimum_should_match":"50%"
    }
    
  }
}


get/forum/article/_search
{
  "query":{
   "dis_max":{
   "queries":[

     {
      "match":{
      "title":{
       "query":"java bengin",
       "mimimum_should_match":"50%"
       "boost":2
    }
    }

     }
   ],
   "tie_breaker":0.9
 }
}
}


14.most_filed

  best-field :尽可能的多个field匹配尽可能多的关键词，同时分数相同的情况下，在一定程度上考虑其他字段，简单来说，
  你对多个field进行搜索，想搜索的某一个field尽可能所得匹配更所关键字
   优点：通过匹配best field策略 ，以及综合考虑其他field还有mimimum——should——match 支持，尽可能多的精确匹配结果到前面
  缺点：除了精确匹配结构，其他结果差不多，排除结果不均匀，没有区分度
  most-fields:z主要是尽可能多个field匹配到某个关键词的doc,优先返回来
   优点：尽可能匹配多尔衮field结果排到前面，排序均匀
   缺点：可能精确匹配的结果，无法到最前面，

GET forum/article/_search
{
  "query": {
    "match": {
      "sub_title": "learning courses"
    }
  }
  
}

GET forum/article/_search
{
  "query": {
    "multi_match": {
      "query": "learning courses",
      "fields": ["sub_title","sub_title.std"],
      "type": "most_fields"
    }
  }
}



如果字段的分词器是  english
 他会把 单词转换成原型，匹配更多的数据
 learning -learn
 couses-couse
不能够精确匹配 ，所以我们使用定义分词器
analyzer
 POST /forum/_mapping/article
{
  "properties": {
      "sub_title": { 
          "type":     "string",
          "analyzer": "english",
          "fields": {
              "std":   { 
                  "type":     "string",
                  "analyzer": "standard"
              }
          }
      }
  }
}


然后尽可能多分匹配多个字段 如fields": ["sub_title","sub_title.std"],



15.most_field 的进行cross-field唯一标识

GET forum/article/_search
{
  "query": {
    "multi_match": {
      "query": "Peter Smith",
      "fields": ["author_first_name", "author_last_name"],
      "type": "most_fields"
    }
  }
  
}
当唯一标识 分配在多个字段
结果并不是我们想的，原因可能是IDF doc1中first_name 出现的频率第，导致分数高，而他排到了前面


 问题。只能多的filed匹配，不能匹配单个field完全匹配
  most-field 没有办法匹配特别少的结构
  IF IDF不准确

 minimum_should_match

　　主要是用来去长尾，long tail长尾，比如你搜索5个关键词，但是很多结果是只匹配1个关键词的，其实跟你想要的结果相差甚远，这些结果就是长尾
minimum_should_match，控制搜索结果的精准度，只有匹配一定数量的关键词的数据，才能返回


16.copy-to
  解决cross-field的问题
    问题1：只是找到尽可能多的field匹配的doc，而不是某个field完全匹配的doc --> 解决，最匹配的document被最先返回

问题2：most_fields，没办法用minimum_should_match去掉长尾数据，就是匹配的特别少的结果 --> 解决，可以使用minimum_should_match去掉长尾数据

问题3：TF/IDF算法，比如Peter Smith和Smith Williams，搜索Peter Smith的时候，由于first_name中很少有Smith的，所以query在所有document中的频率很低，得到的分数很高，可能Smith Williams反而会排在Peter Smith前面 --> 解决，Smith和Peter在一个field了，所以在所有document中出现的次数是均匀的，不会有极端的偏差

  使用copy-to 把多个字段放到一个字段中

   建立映射
   PUT /forum/_mapping/article
{
  "properties": {
      "new_author_first_name": {
          "type":     "string",
          "copy_to":  "new_author_full_name" 
      },
      "new_author_last_name": {
          "type":     "string",
          "copy_to":  "new_author_full_name" 
      },
      "new_author_full_name": {
          "type":     "string"
      }
  }
}

 GET /forum/article/_search
{
  "query": {
    "match": {
      "new_author_full_name":       "Peter Smith"
    }
  }
}
 
 17.cross-field
 GET forum/article/_search
{
  "query": {
    "multi_match": {
      "query": "Peter Smith",
      "fields": ["author_first_name","author_last_name"],
      "operator":"and",
      "type": "cross_fields"
    }
  }
}
  如何解决 问题1：尽可能多的匹配多个field ,而不是单个field  所以使用 
   每一个 term 都在字段中出现

 2.不能去长尾   
 3.IDF/TF  不准确
   这样可以是每一个term计算出来的IDF,去最小值，不会导致极端星狂
    perter  smias

   以为你 first name 出现频率低，IDF的分数就很高，而last name出现频率高，IDF分数正常，不会产生极端情况


18.phrase matching 

  近似搜索
  java spatk不含有任何字符
  java spatk 两个单词老的越近分数越高，排名靠前
  1.phrase match  短语匹配  proximity match 近似匹配
  GET forum/article/_search
{
  "query": {
    "match_phrase": {
      "content": "elasticsearch article"
    }
  }
}

只包java spark的doc返回


term position 

hello world java spark doc1
hi spark java  doc2

hello doc1(0)
wrrld doc1(1)
java doc1(2)doc2(2)
spark doc2(1)

后面的数字就是position

get _analyze
{
  "text":"hello world"
   "analyzer":"srandard"
}


 parase match 原理
  满足下面条件
1.根据 每一个term 去匹配doc,肚子啊doc中后，
2.在去计算postion java 的值要小于spark 1
  所以就找到了 doc1

19.slop
 GET /forum/article/_search
{
  "query": {
    "match_phrase": {
      "content": {
        "query": "java best",
        "slop":4
      }
    }
  }
}
slop 就是移动query term 多少次就可以匹配到

 this is       dog       and         pig
 is   pig

      is/big
       is      big
       is                 big    
       is                               pig

至少移动4此就可以匹配到is big
match_parse 就是短语匹配
proximity parse  近似匹配


20 ,平衡精准度和召回率
这样既可以提高精准度，也可以提高召回率
GET forum/article/_search
{
  "query": {
   "bool": {
     "must": [
       {
          "match": {
      "content": "java  sprak"
    }
       }
     ],
     "should": [
       { 
         "match_phrase": {
            "content": {
               "query": "java  sprak",
               "slop":222
            }
         }
       }
     ]
   }
  } 
}

21.性能优化
GET forum/article/_search
{
  "query": {
     "match": {
       "content": "java spark"
     }
  },
  "rescore":{
    "window_size":50,
    "query":{
      "rescore_query":{
        "match_phrase":{
          "content":{
            "query":"java spark",
            "slop":50
          }
        }
      }
    }
  }
  
}
 由于 match 可能匹配除了200条数数据，但是分页只展示50条，我们就可以进行对前50条进行匹配
window_size  :只在前50条进行近似匹配，

22.
GET my_index/my_type/_search
{
  "query": {
    "regexp":{
      "title":{
        "value":"c.+"
      }
    }
  }
}

GET my_index/my_type/_search
{
  "query": {
    "wildcard": {
      "title": {
        "value": "c?*d*"
      }
    }
  }
}



GET my_index/my_type/_search
{
  "query": {
    "prefix": {
      "title": {
        "value": "c3"
      }
    }
  }
}


1.前准匹配
  性能差，他会匹配所有的doc
2.通配符
3.正则表达式
性能都差，不建议使用  

23.匹配前准 在搜索的时候匹配 search-time
  hello w 
  GET /my_index/my_type/_search
{
  "query": {
    "match_phrase_prefix": {
      "title": {
        "query": "hello w",
        "slop":4,
        "max_expansions": 2
      }
    }
  }
}

1.match hello 的所有 doc
2。匹配 w开头的倒排索引
3.slop 在指定移动范围内获取含有  hello w  的doc
4.max_expansions 匹配到指定的数量的前准就不在匹配


24.ngram 实现 index-time
ngram 是什么
 hello  w

 ngram  legth=1  h e l l o w
 ngram   2    he el ll l0 ow

ngram 3   hel ell ll0


edge ngram 

h
he
hel
hell
hello  doc1

w  doc1
wo
wor
worl
world

hello w
 获取doc1 ,position 也匹配 ，返回 doc1  返回 helloworld

 搜索的时候，不用根据一个前准，然后扫描整个倒排索引 ，简单匹配helo w 
 和match 一样就不用去全文检索，所有所有的倒排索引，直接在建立index建立好就行

设置edge_ngramedge_ngram
PUT /my_index
{
    "settings": {
        "analysis": {
            "filter": {
                "autocomplete_filter": { 
                    "type":     "edge_ngram",
                    "min_gram": 1,
                    "max_gram": 20
                }
            },
            "analyzer": {
                "autocomplete": {
                    "type":      "custom",
                    "tokenizer": "standard",
                    "filter": [
                        "lowercase",
                        "autocomplete_filter" 
                    ]
                }
            }
        }
    }
}
指定 字符安mapping
PUT /my_index/_mapping/my_type
{
  "properties": {
      "title": {
          "type":     "string",
          "analyzer": "autocomplete",
          "search_analyzer": "standard"
      }
  }
}
匹配

GET my_index/my_type/_search
{
  "query": {
    "match_phrase": {
      "title": "hello we"
    }
  }
}
如果用match，只有hello的也会出来，全文检索，只是分数比较低
推荐使用match_phrase，要求每个term都有，而且position刚好靠着1位，符合我们的期望的

25.TF IDF  空间向量

 1.bool model
  过滤出doc不去计算分数
  bool  must must bot should

  2.TF/IDF
    TF:单个term 出现的频率 次数越多分数越高
    IDF:单个term 出现在所有doc中的次数越多，分数越低
  3.length frequency
    每个字段值得长度，越长，分数越低

  4，vector space model
       query vector 

     d多个term 对于所有doc计算一个分数
     hello term在所有的doc总额分数 2
     world term 所有doc总数分数  5
     query  vector[2,5] 
 
   docvector
    会给每一个doc,n那每个term 计算出一个分数来，hello 有一个分数，world有一个分数，在哪所有term分数组成一个doc
    vector
    最后计算 每个doc vectory 计算出对query vector 的弧度
    最后基于这个弧度给出一个docd相对于query 中多个term 的一个分数
    弧度越大 分数越低





27.相关度分数的优化方式

 1.query-time boost
  增强分数
  GET forum/article/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "match": {
            "title": {
              "query": "java",
              "boost":2
            }
          }
        },
        {
          "match": {
            "content": "java"
          }
        }
      ]
    }
  }
}
2,权重的修改
GET  forum/article/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "match": {
            "title": "java"
          }
        },
        {
          "match": {
            "content": "java"
          }
        },
         {
           "bool": {
             "should": [
               {
                 "match": {
                   "sub_title": "earned"
                 }
               }
             ]
           }
         }        
      ]
    }
  }
}

3,降低权重
GET forum/article/_search
{
  "query": {
    "boosting": {
      "positive": {
        "match": {
          "content": "java"
        }
      },
      "negative": {
        "match": {
          "content": "spark"
        }
      
      },
      "negative_boost": 0.2
    }
  }
}

negative_boost：给找到的分数乘以0.2减低分数

4,不计算分数

GET forum/article/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "constant_score": {
           "query": {
             "match": {
               "content": "java"
             }
           }
          }
        }
      ]
    }
  }
}

GET forum/article/_search
{
   "query": {
     "function_score": {
       "query": {
         "multi_match": {
           "query": "java spark",
           "fields": ["title","content"]
         }
       },
       "field_value_factor": {
         "field": "follower_num",
         "modifier": "log1p",
         "factor": 0.2
       },
       "boost_mode": "sum",
        "max_boost": 2  
     }
   }
}

如果field将会对每个doc分数乘以follower_num 如果是0，分数就会变成0
效果不好。
添加 log1p   modeifier :log1p new_score=old_score*log(1+number)
杂家factor 进一步影响分数，newscore=old-score*log(1+fator*number)


boost_mode .可以决定分数和指定字段如何计算 
max_boost 限制计算的分数不找富哦这个值



29纠正错误
fuzzy 在拼写错误的时候可以进行金正
fuziness  允许在某个范围内，太大不起作用

GET  my_index/my_type/_search
{
  "query": {
    "fuzzy": {
      "text":{
        "value": "surprize",
        "fuzziness": 50
      }
    }
  }
}

也可已使用下面方式
 GET my_index/my_type/_search
{
  "query": {
    "match": {
      "text": 
    {
      "query": "SURPIZE ME",
      "fuzziness": "AUTO"
    }
    }
  }
}


30 ik分词器
1、在elasticsearch中安装ik中文分词器

（1）git clone https://github.com/medcl/elasticsearch-analysis-ik
（2）git checkout tags/v5.2.0
（3）mvn package
（4）将target/releases/elasticsearch-analysis-ik-5.2.0.zip拷贝到es/plugins/ik目录下
（5）在es/plugins/ik下对elasticsearch-analysis-ik-5.2.0.zip进行解压缩
（6）重启es
   两种分词器
    ik_max_word:颗粒细，拆分细，
    ik_smart :颗粒度粗，
 GET my_index/my_type/_search
{
  "query": {
    "match": {
      "text": "16岁少女结婚好还是单身好"
    }
  }
}
GET  _analyze
{
  "text": "绝对是空格键绝对是空格键绝zhong'guo'ren",
   "analyzer": "ik_max_word"
}   

31.ik分词器配置详解
   IKAnalyzer.cfg.xml 配置自定义词库
   main.dic ik内置的中文词库 总共 27万
   quantifier.dic 放以一些单位相关词
   surname 中国的形式
   stopworld.dic英文停用词

  ik最终要的两个配置文件
   mian.dic包含原生的中文词语，会按照这个里面的词语分词
   stopword，dic 包含英语通用词
 
2，自定义词库
   建立自己的词库 ，网红 
    custom/mydict.dic
    重启es
    建立停用词库 比如  了  的 啥 不建立索引
    customer/ext_stopword.dic补充停用词



33，深入bucket metric

bucket 相当于分许 group by userid
metricc ：count（*） 对于每个userid 的bucket所有的数据计算一个数量
 当我们有一堆bucket后就可以对每个bucker中的数据进行聚合分词，比如平均数


34.聚合
GET tvs/sales/_search
{
   "size": 0, 
  "aggs": {
    "popular_clour": {
      "terms": {
        "field": "color"
      }
    }
  }
}

aggs固定于法，对一份数据执行分组聚合操作
size 只获取聚合结果，而不要执行聚合的原始数据
popular_clour：聚合名字
terms:根据值进行分组
  "aggregations": {
    "popular_color": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 0,
      "buckets": [
        {
          "key": "红色",
          "doc_count": 4
        },
        {
          "key": "绿色",
          "doc_count": 2
        },
        {
          "key": "蓝色",
          "doc_count": 2
        }
      ]
    }
  }
}
hits.hits 我们指定size是0，所有hits,hits就是空的
aggregations：聚合结果
buckets：指定的field划分的bukets
key 每个bucket 对应的那个孩子
doc_count 数量颜色


按照数量降序排序

GET /tvs/sales/_search
{
  "size": 0,
  "aggs": {
    "group_by_color": {
      "terms": {
        "field": "color"
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

{
  "took": 9,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "failed": 0
  },
  "hits": {
    "total": 8,
    "max_score": 0,
    "hits": []
  },
  "aggregations": {
    "group_by_color": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 0,
      "buckets": [
        {
          "key": "红色",
          "doc_count": 4,
          "avg_price": {
            "value": 3250
          }
        },
        {
          "key": "绿色",
          "doc_count": 2,
          "avg_price": {
            "value": 2100
          }
        },
        {
          "key": "蓝色",
          "doc_count": 2,
          "avg_price": {
            "value": 2000
          }
        }
      ]
    }
  }
}

在颜色下进行品牌的分组计算平均价
GET /tvs/sales/_search
{
  "size": 0,
  "aggs": {
    "group_color": {
      "terms": {
        "field": "color"
      },
      "aggs": {
        "avg_price": {
          "avg": {
            "field": "price"
          }
        },
          "group_brand": {
            "terms": {
              "field": "brand"
            }, 
            "aggs": {
              "avg_brand-price": {
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



GET /tvs/sales/_search
{
  "size": 0,
  "aggs": {
    "gtoup_color": {
      "terms": {
        "field": "color"
      },
      "aggs": {
        "avg_price": {
          "avg": {
            "field": "price"
          }
        },
        "max-price":{
          "max": {
            "field": "price"
          }
        },
        "min-price":{
          "min": {
            "field": "price"
          }
        },
        "sum_price":{
          "sum": {
            "field": "price"
          }
        }
      }
    }
  }
}



38 histogram 类似terms,进行分组，不同的是，
他是按照 interval  的值进行区分的
histogram ：{
  “field”:"price",
  "interval:20000}


 根据price的值属于那个区间进行分组

 GET /tvs/sales/_search
{
  "size": 0,
  "aggs": {
    "price": {
      "histogram": {
        "field": "price",
        "interval": 2000
      },
      "aggs": {
        "sum_price": {
          "sum": {
            "field":"price"
          }
        }
      }
      
    }
  }
  
}

39.date_histogram 


  技术按照date的field以及interval,按照一定的日期间隔
  区划分bucket

   date  interval = 1m
   2018-11-01----2018-11-30
   2018-12-01----2018-12-30

    回去扫描每个数据的date field,判断
    date 落在那个bucket 中。就将其放在哪个bucket中


    min-doc-count 及时没有数据在interval
    区间中，那么这个区间也要返回数据，不然过滤掉这个区间


    extended_bounds min max h划分bucket的
    时候限定其实日期和结束日期

    GET  /tvs/sales/_search
{
  "size":0,
  "aggs": {
    "sales": {
      "date_histogram": {
        "field": "sold_date",
        "interval": "month",
        "format": "yyyy-MM-dd",
        "min_doc_count": 0,
        "extended_bounds":{
        "min":"2016-01-01",
        "max":"2017-12-31"
          
        }
      }
    }
  }
}




GET tvs/sales/_search
{
  "size": 0,
  "aggs": {
    "group_by_date": {
      "date_histogram": {
        "field": "sold_date",
        "interval": "quarter",
        "format": "yyyy-MM-dd",
        "min_doc_count": 0,
        "extended_bounds":{
          "min":"2016-01-01",
          "max":"2017-12-31"
        }
      },
      "aggs": {
        "group_by_brand": {
          "terms": {
            "field": "brand"
          },
          "aggs": {
            "sum-price": {
              "avg": {
                "field": "price"
              }
            }
          }
        },
        "totle_sum":{
          "sum": {
            "field": "price"
          }
        }
      }
    }
  }
  
}

GET tvs/sales/_search
{
  "query": {
    "term": {
      "brand": {
        "value": "小米"
      }
    }
  },
  "aggs": {
    "group_color": {
      "terms": {
        "field": "color"
      }
    }
  }
}
商品长虹品牌和全局品牌的平均价格

GET tvs/sales/_search
{
  "size": 0,
  "query": {
    "term": {
      "brand": {
        "value": "长虹"
      }
    }
  },
  "aggs": {
    "avg_price": {
      "avg": {
        "field": "price"
      }
    },
    "all-price":{
      "global": {}
       , "aggs": {
         "all_brand_avg_price": {
           "avg": {
             "field": "price"
           }
         }
       }
      
    }
  }
  
}

global :就是global bucket 就是讲所有的数据纳入聚合的
scope,而不管之前的query
过滤条件和聚合 
GET  tvs/sales/_search
{
  "size": 0,
  "query": {
    "constant_score": {
      "filter": {
        "range": {
          "price": {
            "gte": 1200
          }
        }
      }
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

tvs/sales/_search
{
   "size":0,
  "query": {
    "term": {
      "brand": {
        "value": "长虹"
      }
    }
  },
  "aggs": {
    "recent_150d": {
      "filter": {
        "range": {
          "sold_date": {
            "gte": "now-1030d"
          }
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
aggs.filter，针对的是聚合去做的

如果放query里面的filter，是全局的，会对所有的数据都有影响

但是，如果，比如说，你要统计，长虹电视，最近1个月的平均值; 最近3个月的平均值; 最近6个月的平均值

bucket filter：对不同的bucket下的aggs，进行filter"

深层进行排序
GET tvs/sales/_search
{
  "size": 0,
  "aggs": {
    "group_by_color": {
      "terms": {
        "field": "color"
      },
      "aggs": {
        "group_brand": {
          "terms": {
            "field": "brand",
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
  }
}


47.深入聚合数据分析 并行聚合算法 ，三角选择算法，近似聚合算法
 1 并行聚合算法
  有些聚合比较适合并行算法，如 max
   max. 每一个节点把最大的值拿到协调节点进行比较，替换最大值
   有些就不爱适合 count（distinct）
2.三角聚合算法
  精准+实时 没有大数据，数据量小。单机可以，
  精准+大数据 hadoop 批处理。非实时，可以处理海量数据，保证精准
  大数据—+实时 es.不准确。可能有误差
3.近似聚合算法
 如果采用。延时在100ms
 如果采用精准算法，延时及时培，es错误

每个季度下品牌进行去重获取结果数量
GET tvs/sales/_search
{
  "size": 0,
  "aggs": {
    "months": {
      "date_histogram": {
        "field": "sold_date",
        "interval": "month"
      },
      "aggs": {
        "distinct_color": {
          "cardinality": {
            "field": "brand"
          }
        }
      }
    }
  }
}


49.cardinality 算法优化

 get tvs/sales/_search
 {
  "size":0.
  "aggs":{
     "distinct_brand":{
        "cardinality":{
          "field":"brand",
          "precision_threshold":100
        }
     }
}

}

precistion 在多少个unque value 以内 cardinality 几乎保证100%准确
cardinality 算法 precision_threshould*8内存消耗

precision值设置越大，占用内存越大，单可以保证100%的准确率
官方 说 100 计算百万数据错误率在5%以内



2 HyperlOGLOG++ hll性能

 cardinality 底层算法 Hll算法

 会对所有的unquie value去hash值 。通过近似值去求distcint count 

 默认情况下，发动cardintiy d的时候，活动太的对所有的field 
 去hash 值，此时将hash值前置，在建立index时候建立

 put /tvs
 {
  "mappings“：{
    ”properties:{
      "brand":{
        "type":"text",
         "fields":{
          "hsah":{
            type:"murmur3"
         }
      }
    }
}
 }
需求：比如有一个网站，记录下了每次请求的访问的耗时，需要统计tp50，tp90，tp99

tp50：50%的请求的耗时最长在多长时间
tp90：90%的请求的耗时最长在多长时间
tp99：99%的请求的耗时最长在多长时间
get tvs/sale/_search
{
  "size：0，
  ”aggs“:{
  "distinct_brand":{
  "cardinality":{
  "field":"brand.hsah
  "prestion-threshould:100 GET /website/logs/_search
{
  "size": 0,
  "aggs": {
    "group-by":{
      "terms": {
        "field": "province"
      },
      "aggs": {
        "latency_percentiles": {
        "percentiles": {
          "field": "latency",
          "percents": [
            50,
            95,
            99
          ]
        }
      },
        "latency_avg":{
          "avg": {
            "field": "latency"
          }
        }
  
      }
    }
    
  }
}
}
}
}

GET /website/logs/_search
{
  "size": 0,
  "aggs": {
    "group-by":{
      "terms": {
        "field": "province"
      },
      "aggs": {
        "latency_percentiles": {
        "percentiles": {
          "field": "latency",
          "percents": [
            50,
            95,
            99
          ]
        }
      },
        "latency_avg":{
          "avg": {
            "field": "latency"
          }
        }
  
      }
    }
    
  }
}

51.percentiles rank
 计算某个范围内的占比

按照品牌分组，计算电视机 销售价在100占比数目
按照省份分组，计算请求时间在100 1000之间的占比
GET website/logs/_search
{
  "size": 0,
  "aggs": {
    "group_by_brand": {
      "terms": {
        "field": "province"
      },
      "aggs": {
        "latency_percentis_Ranks": {
          "percentile_ranks": {
            "field": "latency",
            "values": [
              100,
              1000
            ]
          }
        }
      }
    }
  }
}

percentile 的优化
TDigest算法，用很多节点执行百分比计算，近似估计，有误差，节点越多
越精准
compression 默认100
限制节点数 compression*20=2000个node计算
越大性能越差，精准度越高，内存越多

一个节点占用32字节 100*20*32=64kb

52倒排索引 正排索引

 1，在搜索导数据的时候
 2.那搜索的doc 再去倒排索引搜索，agg-field的值，必须遍历所有的
 倒排索引

 3.如果使用正派索引就不必去遍历所有的值，可能搜索到一半就把所偶遇的
 的agg_field都搜索到了

53.doc value原理
  1，index-time 生成
  put post生成doc value
  2.正派索引也会写入到磁盘，
  os cache会缓存横排索引，当内存不足时候，就会把正派索引进行写入磁盘
  3，性能问题
    给jvm 更少的内存， os  cache更多的内存


 2，column压缩

    1，相同值进行保存单只
    2.少于256值，使用 table encoding
    3.大于256 看有没有公约数，保留最大公约数
    4.没有公约数，采用 offset 压缩方式
 3.如果不需要聚合可以禁用

  put index
  {
    "ampping":{
      "type":{
        "properties":{
          "my-field":{
            "type":"keyworld"
            "doc_valus":false
          }
        }
      }
    }
  }

54. fielddate

 1对于分词的字段，进行聚合包错
 GET /test_index/test_type/_search
{
  "aggs": {
    "group": {
      "terms": {
        "field": "cotntent",
        "size": 10
      }
    }
  }
}
对分词直接聚合报错，你必须打开fielddata,然后将正派索引加入到内存中，才可以对分词field执行聚合操作，会下哈
大量内存

2PUT /test_index/_mapping/test_type
{
  "properties": {
    "content":{
      "type": "text",
      "fielddata": true
    }
  }
}

分词fieldddata 的工作原理
 
 1.不分词的field，那么字建立index-time，就会建立doc value,在进行聚合的时候就可以使用
 doc value
 2,分词的field 建立索引的时候是不进行建立doc value



如果一定要进行分词进行聚合操作就必须，fielddate=true，然后加你聚合索引
的时候，现场就将field对应的数据，建立一份正派索引，和doc values类似
但是只会讲 fielddate正派索引架子啊到内存哪种，然后基于内存中的fielddata正排索引执行分词field的聚合操作如果基于磁盘和os cache，那么性能会很差

55.fileddate 核心原理
 1.filed加载到内存的过程lazy课程大纲

1、fielddata核心原理

fielddata加载到内存的过程是lazy加载的，对一个analzyed field执行聚合时，才会加载，而且是field-level加载的
一个index的一个field，所有doc都会被加载，而不是少数doc
不是index-time创建，是query-time创建

2、fielddata内存限制

indices.fielddata.cache.size: 20%，超出限制，清除内存已有fielddata数据
fielddata占用的内存超出了这个比例的限制，那么就清除掉内存中已有的fielddata数据
默认无限制，限制内存使用，但是会导致频繁evict和reload，大量IO性能损耗，以及内存碎片和gc

3、监控fielddata内存使用

GET /_stats/fielddata?fields=*
GET /_nodes/stats/indices/fielddata?fields=*
GET /_nodes/stats/indices/fielddata?level=indices&fields=*

4、circuit breaker

如果一次query load的feilddata超过总内存，就会oom --> 内存溢出

circuit breaker会估算query要加载的fielddata大小，如果超出总内存，就短路，query直接失败

indices.breaker.fielddata.limit：fielddata的内存限制，默认60%
indices.breaker.request.limit：执行聚合的内存限制，默认40%
indices.breaker.total.limit：综合上面两个，限制在70%以内


56.fileddata 细颗粒
  1min 仅仅加载至少出现1%在doc中，才会去加载到内存中
  比如 某个值 hello  总共有1000doc hello必须在10个
  doc出现，那么这个hello对应的fileddata才会加载到内存

  2.min_segment_size 至少500doc 的segment不加字啊fileddate

  加载fileddata的时候，也按照segment去进行架子啊，某个
  segment里面的doc数量少于500个，哪呢和这个segment
  的fileddata不加字啊到内存中


post index/-mappIng/tyep
{
  "properties:{
  "myfiled":{
  "type：”text“.
   "fileddata":{
   "filter":{
     "frequency":{
     "min":0.01,
     "min-segment_sizesegment_size"
   }
 }
 }
}
}
}


57.建立fileddata记载机制以及标记
 当我们进行聚合索引的时候，会给分析的filed建立正派索引
。是在query time的时候，我们是否可以再建立index-time时候建立索引



   POST  /test_index/_mapping/test_type
{
  "properties": {
    "test_field":{
      "type": "string",
      "fielddata": {
        "loading":"eager"
      }
    
    }
  }
}
 2.当fileddata中有大量的重复字段
  doc1 status1
  doc2 status2
  doc3status2

 建立全局索引 global  ordinal 原理解释


 status1 0
 staus2  1

  doc1 0
  doc2 1
  doc3 1

  建立fileddata 也是这个样子，减少重复字符串出现的次数

POST  test_index/_mapping/test_type
{
  "properties": {
    "test_filed":{
      "type": "string",
       "fielddata": {
         "loading":"eager_global_ordinals"
       }
    }
  }
}


58深度优先 广度优先

 1，深度优先
   比如我们有10万个sctor 最后只要10 actor就可以了

    但是我们深度优先，构架了一棵树，10 万个 actor 每一个actor有 10wan
    个filem,  我们裁剪了大量数据

 2.广度优先
   在最外层进行 建立，只需要在最外层裁剪，后的数据，在进行裁剪filem    

 aggs:{
 actors:{
   ""terms:{
     "filed":"actor",
     "size":10,
     "collect-mode":"breasth_first"
 }
    "aggs":{
      "costtars":{
        "terms":{
          "filed":"films",
          "size":5
        }
      }
    }
}
}

60数据建模
优点：数据不冗余，维护方便
缺点：应用层join，如果关联数据过多，导致查询过大，性能很差


path_hierarchy tokenizer讲解

/a/b/c/d --> path_hierarchy -> /a/b/c/d, /a/b/c, /a/b, /a

PUT /fs 
{
  "settings": {
    "analysis": {
      "analyzer": {
        "paths":{
          "tokenizer":"path_hierarchy" //fenci
        }
      }
    }
  }
}

PUT /fs/_mapping/file
{
  "properties": {
    "name":{
      "type": "keyword"
    },
    "path":{
      "type": "keyword",
      "fields": {
        "tree":{
          "type": "text",
          "analyzer": "paths"
        }
      }
    }
  }
}

64.全局锁
  数据建模 ，3中颗粒度
  最粗的一种全局锁
  乐观锁
   多个线程去修改doc,他的version 已经修改，就会拿最新的vsersion 最后修改
  悲观岁
  只有你可以进行操作，其他人不能进行操作

  1.全局锁
  put /fs/lock/global/_create
  {}
  fs:你要上锁的index
  lock：就是制定的全局锁的index的type
  gloabl:全局锁对应的这个doc的id
  _create 强制必须创建，如果所已经崔存在，就会报错

   DELETE /fs/lock/global


  3、全局锁的优点和缺点

优点：操作非常简单，非常容易使用，成本低
缺点：你直接就把整个index给上锁了，这个时候对index中所有的doc的操作，都会被block住，导致整个系统的并发能力很低

上锁解锁的操作不是频繁，然后每次上锁之后，执行的操作的耗时不会太长，用这种方式，方便 


65.document悲观锁

  全局锁 一次性就锁住整个index,对于整个index所有的增删改查，block，如果不频繁，还可以

  更细颗粒度的锁，document锁，每次操作的你要执行增删改查的doc,doc锁住，其他线程就不能对
  起操作，只是锁了部分线程，其他线程还可以进行增删改操作的，

  document锁，是用脚本进行上锁

POST /fs/lock/1/_update
{
  "upsert": { "process_id": 123 },
  "script": "if ( ctx._source.process_id != process_id ) { assert false }; ctx.op = 'noop';"
  "params": {
    "process_id": 123
  }
}



 fs/lock 是固定的，就是fs下lock type 专门进行上锁
 fs/lock/id 比如1,就是上锁的doc


 1.当不存在所得时候就进行添加 upsert
  创建了一条 fs/lock/1  ,为param设置 process_id =123
 2当发现上锁了，就执行 upfdate操作，scrip就会对比，procss_id r
 如果相同就是同一个进程，可以操作，如果不通就报错



put  fs/lock/_bulk
{"delete":{"_id":1}}


delete fs/lock/1

66.基于共享锁，和排他锁实现悲观锁并发操作
 共享锁：数据是共享的，多个线程过来，都可以共享一个数据
  ，然后执行度操作
  排他锁：排他操作，只能一个线程，获取增删改操作
   读写分离
  1.当读取数据的时候，任意线程都可以同时进行读取数据，每个
  线程都可以上一个共享锁，，如果其他线程
  进行修改，如果已经上了共享锁，就不允许上排他锁们必须等待

  则，如果有人度数据，就不允许修改数据

  2，如果数据修改，就上排他锁，
  那么其他线程过来要修改数据，也尝试上排他锁，此时岁长途，必须等待，
  如果有人读操作，也不允许失败，共享锁，排他锁 互斥

  则，在修改的数据，不允许其他人膝盖数据妈也不允许读数据，

加共享锁
POST /fs/lock/1/_update 
{
  "upsert": { 
    "lock_type":  "shared",
    "lock_count": 1
  },
  "script": {
    "lang": "groovy",
    "file": "judge-lock-2"
  }
}

3、对共享锁进行解锁

POST /fs/lock/1/_update
{
  "script": {
    "lang": "groovy",
    "file": "unlock-shared"
  }
}

排他锁
PUT /fs/lock/1/_create
{ "lock_type": "exclusive" }

5、解锁排他锁

DELETE /fs/lock/1

67.netesd
object类型数据结构的底层存储。。。

{
  "title":            [ "花无缺", "发表", "一篇", "帖子" ],
  "content":             [ "我", "是", "花无缺", "大家", "要不要", "考虑", "一下", "投资", "房产", "买", "股票", "事情" ],
  "tags":             [ "投资", "理财" ],
  "comments.name":    [ "小鱼儿", "黄药师" ],
  "comments.comment": [ "什么", "股票", "推荐", "我", "喜欢", "投资", "房产", "风险", "收益", "大" ],
  "comments.age":     [ 28, 31 ],
  "comments.stars":   [ 4, 5 ],
  "comments.date":    [ 2016-09-01, 2016-10-22 ]


GET /website/blogs/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "comments.name": "黄药师" }},
        { "match": { "comments.age":  28      }} 
      ]
    }
  }
}

修改mapping，将comments的类型从object设置为nested

PUT /website
{
  "mappings": {
    "blogs": {
      "properties": {
        "comments": {
          "type": "nested", 
          "properties": {
            "name":    { "type": "string"  },
            "comment": { "type": "string"  },
            "age":     { "type": "short"   },
            "stars":   { "type": "short"   },
            "date":    { "type": "date"    }
          }
        }
      }
    }
  }
}

{ 
  "comments.name":    [ "小鱼儿" ],
  "comments.comment": [ "什么", "股票", "推荐" ],
  "comments.age":     [ 28 ],
  "comments.stars":   [ 4 ],
  "comments.date":    [ 2014-09-01 ]
}
{ 
  "comments.name":    [ "黄药师" ],
  "comments.comment": [ "我", "喜欢", "投资", "房产", "风险", "收益", "大" ],
  "comments.age":     [ 31 ],
  "comments.stars":   [ 5 ],
  "comments.date":    [ 2014-10-22 ]
}
{ 
  "title":            [ "花无缺", "发表", "一篇", "帖子" ],
  "body":             [ "我", "是", "花无缺", "大家", "要不要", "考虑", "一下", "投资", "房产", "买", "股票", "事情" ],
  "tags":             [ "投资", "理财" ]
}
GET /website/blogs/_search
{
  "size": 0,
  "aggs": {
    "comments_path": {
      "nested": {
        "path": "comments"
      },
      "aggs": {
        "group_age": {
          "histogram": {
            "field": "comments.age",
            "interval": 10
          },
          "aggs": {
            "revers_path": {
              "reverse_nested": {
               
              },
              "aggs": {
                "group_tag": {
                  "terms": {
                    "field": "tags.keyword"
                  }
                }
              }
            }
          }
        }
      }
    }
  }

  GET /website/blogs/_search
{
  "size": 0,
  "aggs": {
    "comments-path": {
      "nested": {
        "path": "comments"
      },
      "aggs": {
        "group_by_date": {
          "date_histogram": {
            "field": "comments.date",
            "interval": "month",
            "format": "yyyy-MM-dd"
          },
          "aggs": {
            "group_start": {
              "avg": {
                "field": "comments.stars"
              }
            }
          }
        }
      }
    }
  }
}

69.父子关系

  nested  objext 导致数据冗余，将多个数据放在一起，不容易维护

  使用父子关系，将多个实体分割，一对多关系，更新一个不影响其他
  维护方便，性能比较好，es会自动处理底层关联关系，并通过一些手段，保证
  搜索性能

  要点，保证父子数据再通一个shard
  父子关系数据存在一个shard中，而且还有映射其关联关系的元数据，那么搜索父子关系数据的时候，不用跨分片，一个分片本地自己就搞定了，性能当然高咯


shard路由的时候，id=1的rd_center doc，默认会根据id进行路由，到某一个shard

PUT /company/employee/1?parent=1 
{
  "name":  "张三",
  "birthday":   "1970-10-24",
  "hobby": "爬山"
}
维护父子关系的核心，parent=1，指定了这个数据的父doc的id

此时，parent-child关系，就确保了说，父doc和子doc都是保存在一个shard上的。内部原理还是doc routing，employee和rd_center的数据，都会用parent id作为routing，这样就会到一个shard

就不会根据id=1的employee doc的id进行路由了，而是根据parent=1进行路由，会根据父doc的id进行路由，那么就可以通过底层的路由机制，保证父子数据存在于一个shard中

GET /company/employee/_search
{
  "query": {
    "has_parent": {
      "parent_type": "rd_center",
      "query": {
        "match": {
          "country.keyword": "中国"
        }
      }
    }
  }
}



GET /company/rd_center/_search
{
  "query": {
    "has_child": {
      "type": "employee",
      "min_children": 2, 
      "query": {
        "match_all": {}
      }
    }
  }
}


GET /company/rd_center/_search
{
  "query": {
    "has_child": {
      "type": "employee",
      "query": {
        "match": {
          "name": "张三"
        }
      }
    }
  }
}

GET /company/rd_center/_search
{
  "query": {
    "has_child": {
      "type": "employee",
      "query": {
        "range": {
          "birthday": {
            "gte": "1980-01-01"
          }
        }
      }
    }
  }
}

























