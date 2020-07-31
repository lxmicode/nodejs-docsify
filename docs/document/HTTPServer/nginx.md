## nginx编译和安装
- 1

## 实际问题与解决
### 代理泛域名
- es:代理 *.lxmicode.com所有二级域名
```nginx
server {
    listen       80;
    server_name ~^([\w-]+)\.lxmicode\.com$;
    location / {
        proxy_set_header        X-Real-IP $remote_addr;
        proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header        Host $http_host;
        proxy_set_header        X-NginX-Proxy true;
        proxy_pass              http://127.0.0.1/$1$request_uri;
    }
}
```

## 参考
-[xuexb.github.io](https://xuexb.github.io/learn-nginx/example/domain.html#%E5%AD%90%E5%9F%9F%E5%90%8D%E8%BD%AC%E5%8F%91%E5%88%B0%E5%AD%90%E7%9B%AE%E5%BD%95)
