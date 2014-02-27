---
layout: post
title: elasticsearch 相关笔记
tags: [elasticsearch]
---

这段时间看了下elasticsearch，做了一些零散的笔记：

**添加logstash加模板**
{% highlight bash%}
 curl -XPUT http://host:port/_template/template-logstash -d '
{
 
    "template" : "logstash-*",
    "settings" : {
      "index.number_of_shards" : 5,
      "index" : {
        "store" : {
          "compress" : {
            "stored" : true,
            "tv": true
          }                                                                                                                                                                                                                       
        }  
      }  
    }, 
    "mappings" : {  
      "fluentd" : {
        "_source" : {
          "compress" : true
        }, 
        "_all" : {
          "enabled" : false
        }, 
        "properties" : {
          "@fields" : {
            "dynamic" : "true",
            "properties" : {
              "type" : {
                "type" : "string",
                "index" : "not_analyzed"
              },
              "result": {
                "type": "string",
                "index": "not_analyzed"
               }   
            }  
          }  
        }  
      }  
    }  

}
' 
{% endhighlight %}



**查询**的时候slash(/)要转义,因为:this is due to regular expression support in the query parser since lucene 4.0 the slash indicates a regexp



**install**:   

+   打开文件数:
increase the number of open files descriptors on the machine , 32K or even 64K

+   内存
bootstrap.mlockall=true, 用mlockall 锁住进程空间,不会被交换到硬盘， 默认最多使用1G内存,最少使用256M


**java service wrapper**


**名词解释**:  

+   **index**: like 'database', has mapping which define multiple types
+   **mapping**: 'schema definition', Each index has a mapping, which defines each type within the index, plus a number of index-wide settings.
   
+   **source field**:the JSON document that you index will be stored in the _source field and will be returned by all get and search requests. This allows you access to the original object directly from search results, rather than requiring a second step to retrieve the object from an ID.
   
+   **id**:The ID of a document identifies a document. The index/type/id of a document must be unique,can get document detail through url
   
+   **cluster**:A cluster consists of one or more nodes which share the same cluster name. Each cluster has a single master node which is chosen automatically by the cluster and which can be replaced if the current master node fails.
   
+   **node**:A node is a running instance of elasticsearch which belongs to a cluster. Multiple nodes can be started on a single server for testing purposes, but usually you should have one node per server.
   
+   **shard**:A shard is a single Lucene instance.Elasticsearch distributes shards amongst all nodes in the cluster, and can move shards automatically from one node to another in the case of node failure, or the addition of new nodes.

一个cluster有1个或多个nodes，shards在所有的nodes上实现负载均衡.shards分primary和replicas

**resources**   
[生产环境使用elasticsearch遇到的一些问题以及解决方法（不断更新）](http://blog.csdn.net/laigood/article/details/8193170)    
[ElasticSearch Cache Usage](http://blog.sematext.com/2012/05/17/elasticsearch-cache-usage/)   

