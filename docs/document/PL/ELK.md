#  ELK elasticsearch+kibana+logstash

## 环境
- 分词工具-IK
  - ik_max_word
  - ik_smart
- kibana开发工具下测试代码(xxx/app/dev_tools#/console)

## elasticsearch
- 搜索引擎

### _analyze
- 测试分词
```bash
POST /_analyze
{
  "text":"欧力给",
  "analyzer":"ik_max_word"
}
```

### 搜索
- [参考-elastic](https://www.elastic.co/guide/cn/elasticsearch/guide/current/search-lite.html)
```txt
# 查询info字段
GET /lxmicode/_search?q=info:赵云

# 查询在info&&mail 同时包含内容 
# info:五虎将+info:赵云
GET /_search?q=%2Bname%3Ajohn+%2Btweet%3Amary
```


### 索引库操作
- put /doc_name

```bash
# put /lxmicode
{
  "mappings": {
    "properties": {
      "info": {
        "type": "text", //text需要分词
        "analyzer": "ik_max_word" //分词器
      },
      "email": {
        "type": "keyword",
        "index": false //不建立索引，后期筛选不了
      },
      "name": {
        "properties": {//多属性嵌套 - properties
          "firstName": {
            "type": "keyword"
          },
          "lastName": {
            "type": "keyword"
          }
        }
      }
    }
  }
}
```

- get /doc_name 
- delete /doc_name  
- update /doc_name/_mapping

```bash
# update /lxmicode/_mapping
{
  properties": {
      "info": {
        "type": "text",
        "analyzer": "ik_max_word"
      }
  }
}
```

### 文档操作
- post /doc_name/_doc/{id} 新增
- get /doc_name/_doc/{id} 查询
- delete /doc_name/_doc/{id} 删除

```bash
# 新增
# post /lxmicode/_doc/1
{
  "info":"赵云小鲜肉，你的身材好棒棒，武功很欧力给！",
  "email":"chaoyun@muji.com",
  "name":{
    "fistName":"张",
    "lastName":"飞"
  }
}
```

- put /doc_name/_doc/{id} 全量修改，先删除后新增

```bash
# post /lxmicode/_doc/1
{
  "info":"赵云小鲜肉，你真的身材好棒棒，武功很欧力给！",
  "email":"zhaoyun@muji.com",
  "name":{
    "fistName":"张",
    "lastName":"飞"
  }
}
```

- post /doc_name/_doc/{id} 增量修改
```bash
# post /lxmicode/_update/1
{
  "doc":{
    "email":"zhaoyun@muji.com"
  }
}
```
