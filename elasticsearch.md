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

































