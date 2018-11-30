## 问题描述

服务通过nginx向后端做代理,且后端代理为域名形式,nginx会缓存域名对应的ip信息,如果后端服务因故障或者迁移,导致ip失效,做代理请求时依然会请求到失效ip,服务调用失败

## 解决方案

1.重启或reload nginx,相对原始,需要代理方通知

2.配置nginx的dns缓存时间, 配置resolver,示例:
```
resolver 114.114.114.114 8.8.8.8 valid=3600s;
resolver_timeout 3s;
resolver 可以在http全局设定，也可在server里面设定
resolver 后面指定DNS服务器，可以指定多个，空格隔开
valid 设置DNS缓存失效时间，自己根据情况判断，建议600以上
resolver_timeout 指定解析域名时，DNS服务器的超时时间，建议3秒左右
```

3.注意在代理到后端域名时，千万不要直接写在proxy_pass中，因为server中使用了resolver，所以必须先把域名定义到一个变量里面，然后在 proxy_pass http://$变量名，否则nginx语法检测一直会报错，提示解析不了域名