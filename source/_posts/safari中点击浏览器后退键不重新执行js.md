---
title: safari中点击浏览器后退键不重新执行js
date: 2019-12-09 15:04:44
categories:
- FE
tags:
- ios
---

### 现象
1. 在safari中访问一个页面1
2. 点击页面上某个链接跳到新页面2
3. 再点击safari的返回键，返回页面1，不会重新执行页面1的js

### 原因
由于Safari的**前进后退键缓存（back-forward cache， bfCache）**造成，和普通缓存html不一样，当页面通过前进后退键加载，则onload事件不会被触发，课通过onpageshow事件监听到页面展示

### 解决方法
```js
window.onpageshow = function(event) {
// presisted 在首次加载页面时为false， 当页面是从bfCache中加载，则为true
  if (event.persisted) {
    alert("From back / forward cache.");
    window.location.reload();
  }
};
```

