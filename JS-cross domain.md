---
title: 跨域
tags: ["跨域"]
date: 2017-09-14 17:54:00
categories:
- 前端
- JS
- 跨域
---
> 偷得半日闲，那就唠一唠JS跨域那些事儿...

<!-- more -->
## 什么是跨域
从一个域名的网页请求另一个域名的资源过程中，协议，域名，端口有任何一个的不同，就被当作是跨域。 

## 跨域的实现方式
### JSONP
JSONP跨域的的原理是利用script标签的src属性请求资源不受同源策略的影响。传入callback名，后端传回来的数据就形似函数的调用。下面直接上代码。  
浏览器端端：
```html
<script>
    var script = document.createElement('script');
    script.type = 'text/javascript';
    // 传参并指定回调执行函数为cbName
    script.src = 'http://www.domain2.com:8080/login?user=admin&callback=cbName';
    document.head.appendChild(script);
    // 回调执行函数
    function cbName(res) {
      alert(JSON.stringify(res));
    }
 </script>
```
node服务器端：
```js
var querystring = require('querystring');
var http = require('http');
var server = http.createServer();
server.on('request', function(req, res) {
    var params = qs.parse(req.url.split('?')[1]);
    // 得到回调函数名
    var fn = params.callback;
    // jsonp返回设置
    res.writeHead(200, { 'Content-Type': 'text/javascript' });
    res.write(fn + '(' + JSON.stringify(params) + ')');
    res.end();
});
server.listen('8080');
```

### 跨域资源共享（CORS）
普通跨域请求：只服务端设置Access-Control-Allow-Origin即可，前端无须设置。  
带cookie请求：前后端都需要设置字段，另外需注意：所带cookie为跨域请求接口所在域的cookie，而非当前页。  
浏览器端：
```js
var xhr = new XMLHttpRequest(); // IE8/9需用window.XDomainRequest兼容
// 前端设置是否带cookie
xhr.withCredentials = true;
xhr.open('post', 'http://www.domain2.com:8080/login', true);
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.send('user=admin');
xhr.onreadystatechange = function() {
    if (xhr.readyState == 4 && xhr.status == 200) {
      alert(xhr.responseText);
    }
};
```
node服务器端
```js
// ...
res.writeHead(200, {
  'Access-Control-Allow-Credentials': 'true',     // 后端允许发送Cookie
  'Access-Control-Allow-Origin': 'http://www.domain1.com',    // 允许访问的域（协议+域名+端口）
  'Set-Cookie': 'l=a123456;Path=/;Domain=www.domain2.com;HttpOnly'   // HttpOnly:脚本无法读取cookie
});
// ...
```

### ServerProxy服务器代理
其原理是利用自建的服务器作为中转站，帮助浏览器请求不同域的资源。  
node实现：  
```js
const url = require('url')
const http = require('http')
const https = require('https')

const server = http.createServer((req,res)=>{
  const path = url.parse(req.url).path.slice(1)
  if(path === 'topics'){
    // node请求真正需要的资源
    https.get('https://cnodejs.org/api/v1/topics', (resp) => {
      let data = ''
      resp.on('data',chunk=>{
        data += chunk
      })
      resp.on('end',()=>{
        res.writeHead(200,{
          'Content-Type':'application/json;charset=utf-8'
        })
        // 获取到的数据返回给浏览器端
        res.end(data)
      })
    })
  }
}).listen(3000,'127.0.0.1')
```

### postMessage
postMessage 主要包含两个 API：  
1). 消息监听：onmessage  
2). 消息发送：postMessage  
#### 监听发送过来的消息
```js
function onMessage(e) {
  console.log(e, e.data);
    // 消息来源安全验证
  if(e.origin !="http://lzw.me") {
    return false;
  }
  // 消息处理...
window.addEventListener('message', onMessage, false);
```
#### 向其他窗体发送消息
```js
// 首先获取要传送消息的窗体对象（如iframe），然后向该对象发送信息
var iframeWin = document.getElementsByTagName('iframe')[0].contentWindow;
iframeWin.postMessage('hello world!', "*");
```