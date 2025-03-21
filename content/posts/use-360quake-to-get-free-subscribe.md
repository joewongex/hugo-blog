+++
title = '使用360QUAKE搜索获取免费订阅'
date = 2025-03-21T17:25:00+08:00
draft = false
+++

1. 打开 https://quake.360.net/quake/#/index，没有账号需要先注册

2. 在搜索栏输入`response:"DOMAIN-SUFFIX"`，response表示搜索响应结果中的内容，DOMAIN-SUFFIX是clash标志性的规则配置名。
搜索语法详见：https://quake.360.net/quake/#/help

3. 在搜索结果中，切换到“经典模式”，查看“http”标签中的内容，找到有clash配置文件内容的条目即可。有些条目查看需要实名认证，忽略即可。

4. 例如有一个搜索结果中“http”标签的内容如下：
```json
{
  "x_powered_by": "",
  "server": "nginx/1.22.1",
  "path": "/subs/clash/aabc6faf74c0a0940504ac6f6547eb0c",
  "body": "xxxx"
}
```
其中`path`的内容就是请求的路径，拼上搜索结果中的域名，就可以得到完整的订阅链接了，直接导入到clash中即可使用。