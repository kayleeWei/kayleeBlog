---
title: Node学习（一）
date: 2019-09-23 17:33:32
categories:
- Node
tags:
- Node.js
---

## 第二章 模块机制

### 2.2 Node的模块实现

1. 在Node中引入模块，需经历3个步骤
  * 路径分析
  * 文件定位
  * 编译执行

2. Node中的模块非为两类：
  * Node提供的模块--核心模块：省略文件定位和编译执行的步骤，且在路径分析中优先判断，加载速度最快
  * 用户编写的模块--文件模块：需完整的路径分析、文件定位、编译执行的过程，速度比核心模块慢
  
3. Node对引入过的模块会进行缓存，减少二次引入时的开销，Node缓存的是编译和执行之后的对象；不论核心模块还是文件模块，require()方法对相同模块的二次加载都采用缓存优先的方式，但核心模块的缓存检查先于文件模块的缓存检查；
