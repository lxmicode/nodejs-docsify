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

