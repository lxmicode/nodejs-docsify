## postman牛逼(进阶使用)

### 变量使用

### 可视化响应-visualizer  
- 语法
```javascript
var template = `html模板`;
pm.visualizer.set(template, {
    response: pm.response.json()
});
```

- 简单demo(Tests标签中)
```javascript
pm.visualizer.set(`
<img src="data:image/gif;base64,{{response.img}}" />
`, {
    response: pm.response.json()
});
```

- 结果

- 官方例子[导入demo](https://app.getpostman.com/run-collection/4e3ee3d03f6e2e7fc250?_ga=2.59246893.1882791416.1610887768-1850767576.1610887768)。
- 官方例子[例子文档](https://learning.postman.com/docs/sending-requests/visualizer/#adding-visualizer-code)

