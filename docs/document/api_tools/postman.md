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

### json 添加注释
-  [代码来源](https://learnku.com/articles/66387)
- postmain - Pre-request Script

```javascript
//  去除json参数注释方法
GlobalJsonMinify = function (json) {

    var tokenizer = /"|(\/\*)|(\*\/)|(\/\/)|\n|\r|\[|]/g,
        in_string = false,
        in_multiline_comment = false,
        in_singleline_comment = false,
        tmp, tmp2, new_str = [], ns = 0, from = 0, lc, rc,
        prevFrom
    ;

    tokenizer.lastIndex = 0;

    while ( tmp = tokenizer.exec(json) ) {
        lc = RegExp.leftContext;
        rc = RegExp.rightContext;
        if (!in_multiline_comment && !in_singleline_comment) {
            tmp2 = lc.substring(from);
            if (!in_string) {
                tmp2 = tmp2.replace(/(\n|\r|\s)*/g,"");
            }
            new_str[ns++] = tmp2;
        }
        prevFrom = from;
        from = tokenizer.lastIndex;

        // found a " character, and we're not currently in
        // a comment? check for previous `\` escaping immediately
        // leftward adjacent to this match
        if (tmp[0] === "\"" && !in_multiline_comment && !in_singleline_comment) {
            // limit left-context matching to only go back
            // to the position of the last token match
            //
            // see: https://github.com/getify/JSON.minify/issues/64
            lc.lastIndex = prevFrom;

            // perform leftward adjacent escaping match
            tmp2 = lc.match(/(\\)*$/);
            // start of string with ", or unescaped " character found to end string?
            if (!in_string || !tmp2 || (tmp2[0].length % 2) === 0) {
                in_string = !in_string;
            }
            from--; // include " character in next catch
            rc = json.substring(from);
        }
        else if (tmp[0] === "/*" && !in_string && !in_multiline_comment && !in_singleline_comment) {
            in_multiline_comment = true;
        }
        else if (tmp[0] === "*/" && !in_string && in_multiline_comment && !in_singleline_comment) {
            in_multiline_comment = false;
        }
        else if (tmp[0] === "//" && !in_string && !in_multiline_comment && !in_singleline_comment) {
            in_singleline_comment = true;
        }
        else if ((tmp[0] === "\n" || tmp[0] === "\r") && !in_string && !in_multiline_comment && in_singleline_comment) {
            in_singleline_comment = false;
        }
        else if (!in_multiline_comment && !in_singleline_comment && !(/\n|\r|\s/.test(tmp[0]))) {
            new_str[ns++] = tmp[0];
        }
    }
    new_str[ns++] = rc;
    return new_str.join("");
};

pm.request.body.raw = GlobalJsonMinify(pm.request.body.raw)
```
