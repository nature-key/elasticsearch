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
    

























