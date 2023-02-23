# 同源策略

同源策略（Same Origin Policy）是浏览器最基本的内容安全策略，它限制了非“同源”资源间的访问。

所谓同源是指：**协议、域名和端口**相同，以`https://www.baidu.com`为例，检测表格中的地址是否符合同源策略：

| URL                          | 是否同源 | 原因                       |
| ---------------------------- | -------- | -------------------------- |
| `https://map.baidu.com`      | 否       | 域名不同（二级域名不相同） |
| `http://www.baidu.com`       | 否       | 协议不同                   |
| `http://www.baidu.com:8081`  | 否       | 端口不一致                 |
| `http://www.baidu.com/index` | 是       | 协议、域名、端口都一致     |

## 限制范围

了解完同源策略的定义，我们需要知道同源策略限制了什么？

1. 无法读取`Cookie`、`LocalStorage`和`IndexDB`
2. 无法读取、修改`DOM`
3. 无法发送`Ajax`

当然，有一些资源的访问是不受限制的：

1. **Iframes**:`<iframe>`允许访问跨域内容，但是`DOM`的访问和操作是被禁止的
2. **CSS**: 可以使用`<link>`标签加载跨域样式资源
3. **Form**: 允许跨域提交form表单
4. **Images**: 允许使用`<img/>`标签加载跨域内容，但禁止使用`canvas`读取跨域图片内容
5. **Multimedie**: 可以使用`<video> `、`<audio>`这类标签加载多媒体资源
6. **Script**: 允许使用`<scipt>`标签加载跨域脚本

## 跨域的解决

虽然同源策略给我们带来了安全感，但在某些开发场景中不免会带来一些麻烦，那如何绕过这些限制呢？

### Iframe

### Jsonp

### Websocket

### Cors（Cross-Origin Resource Sharing）

> [MDN Cross-Origin Resource Sharing](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

## 参考

1. [内容安全政策](https://developers.google.com/web/fundamentals/security/csp)
2. [Same-origin policy](https://web.dev/same-origin-policy/)
3. [浏览器同源政策及其规避方法](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)
4. [浏览器同源策略及跨域的解决方法](https://juejin.cn/post/6844903681683357710)
