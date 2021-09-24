## postman牛逼(进阶使用)
- 一线代码书写位置 *Tests标签*

### 变量
- 局部变量
```javascript
#设置token到全局变量
var data = JSON.parse(responseBody);
pm.environment.set("token", data.uuid);
```
- 全局变量
```javascript
#设置token到全局变量
var data = JSON.parse(responseBody);
pm.globals.set("token", data.uuid);
```

- 使用
- {{变量名}}
- 使用范围,基本全覆盖，包括body-raw(主要json/post/get..)
```json
{
    "token": "{{token}}"
}
```
- 实际场景(JTW)
```javascript
#设置变量
var data = JSON.parse(responseBody);
pm.environment.set("token", data.uuid);
#使用
#authorization -> type -> Bearer Token ->Token ->{{token}}
```

### 可视化响应-visualizer
- 语法
```javascript
var template = `html模板`;
pm.visualizer.set(template, {
    response: pm.response.json()
});
```

- 实际场景(Base64验证码)
```javascript
pm.visualizer.set(`
<img src="data:image/gif;base64,{{response.img}}" />
`, {
    response: pm.response.json()
});
```

- 结果可以在 Body->visualiz
- 官方例子[导入demo](https://app.getpostman.com/run-collection/4e3ee3d03f6e2e7fc250?_ga=2.59246893.1882791416.1610887768-1850767576.1610887768)。
- 官方例子[例子文档](https://learning.postman.com/docs/sending-requests/visualizer/#adding-visualizer-code)


### Base64
- 加密
```javascript
var wordArray = CryptoJS.enc.Utf8.parse(字符串);
var enStr = CryptoJS.enc.Base64.stringify(wordArray);
```
- 解密
```javascript
var wordArray = CryptoJS.enc.Base64.parse(加密字符串);
var decodedString = CryptoJS.enc.Utf8.stringify(wordArray);
```
