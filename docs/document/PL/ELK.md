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
