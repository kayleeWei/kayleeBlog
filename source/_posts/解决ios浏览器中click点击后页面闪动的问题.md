---
title: 解决ios浏览器中click点击后页面闪动的问题
date: 2019-12-09 14:49:30
categories:
- FE
tags:
- ios
---

### 现象：

在ios浏览器中的页面，点击页面后出现dom背景闪动（灰色半透明的背景）

### 解决方法：

为闪动dom添加以下css解决， 将ios默认点击交互效果的透明度设为0

```css
.class{
   -webkit-tap-highlight-color:transparent; //方法一
   -webkit-tap-highlight-color: rgba(0, 0, 0, 0); //方法二
   tap-highlight-color: rgba(0, 0, 0, 0);//方法三
}
```
