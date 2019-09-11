---
title: '部分浏览器在post请求头部添加origin: null'
tags:
  - post
  - fetch
  - origin
  - referrerPolicy
categories:
  - FE
date: 2019-09-10 19:51:46
---


## 现象描述：

在部分安卓手机的UC浏览器、QQ浏览器和自带浏览器中，当发起post请求时，浏览器会自动给请求头部增加origin: null，而后端出于安全考虑，无法处理origin：null的请求导致请求失败。

## 解决方法：

### 方法一：在client和server之间加一层请求转发，以node为例，通过node层转发java请求

在node层转发java请求时，手动增加origin: 'xxx'字段，以express为例：

```js
// node层
router.post(`/api/*`, async (req, res) => {
  const url = req.originalUrl.substring(req.originalUrl.indexOf(`/api/`) + 5);
  const cookies = req.cookies;
  const resp = await fetch('xxxx(请求的url)', {
    method: 'post',
    body: queryString.stringify(Object.assign({}, req.body)),
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded',
      origin: 'https://xxx(需要设置的origin)', // 添加origin
      cookie: Object.keys(cookies).map(c => `${c}=${cookies[c]}`).join(';'),
    },
  });
  const retJson = await resp.json();
  res.send(retJson);
})

// 请求node接口
const req = fetch(`${当前前端域名}/api/${接口名}`, {
  method: 'POST',
  ...
})

req.then((res) => {
  ...
})
```
从安全角度考虑，不推荐从node层转发时添加origin方法，可参考方法二

### 方法二：请求java API时请求头部增加referrerPolicy: 'origin'

#### 为什么有些浏览器会将origin设置为null呢？

根据[fetch文档](https://fetch.spec.whatwg.org/#origin-header)，如果请求不是get或者head，则referrerPolicy设置为'no-referrer'时。会将origin设置为null，因此将referrerPolicy设置为'origin'，就不会出现origin：null的情况，解决方法如下：

![WeChat15ff80a221717be883112c23223d90db.png](https://i.loli.net/2019/09/10/RmHlyYuDBVXhx6p.png)

```js
// 请求java API
const req = fetch(`${java API}`, {
  method: 'POST',
  referrerPolicy: 'origin',
  ...
})
```