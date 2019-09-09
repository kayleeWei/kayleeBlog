---
title: 当input标签的maxlength属性遇上emoji
date: 2019-09-06 14:25:42
categories:
- FE
tags:
- HTML
- react
---

限制输入框的字数是一个很常见的需求，我们常常使用input/textarea标签的maxlength属性来限制输入字数。但当用户输入emoji字符时，在不同端中maxlength属性计算出的emoji长度却不一致。js和安卓上一个emoji都算作2个字符长度，而在ios上为1个字符长度。

## 用js而非maxlength限制字数

为了让限制字数在不同端上得到统一，可以用js限制字数取代maxlength属性，使用扩展运算符将输入字符串都转为数组计算长度，这样emoji在不同端长度都是1：
```js
  const inputValue = '👿😄other';
  const inputArr = [...inputValue]; // ["👿", "😄", "o", "t", "h", "e", "r"]
  console.log(inputArr.length) // 7
```

当超出限制长度时，我们就将字符串截断（slice等操作），只展示限制字数内的内容。

## 使用js限制输入长度带来的问题

### 使用中文输入法时的字符截断问题

但用js限制长度时，会出现如图问题：

假设限制长度为2，我们想输入“例子”这两个字，在使用中文输入法时“li”处就会判断已经达到2个字符，因此在输入域内只能展示“li”，不能继续输入。

![1.png](https://i.loli.net/2019/09/06/2vajde8EwBGQCsg.png)

### 解决办法 — compositionEvent

使用compositionEvent事件来监听中文输入的过程，compositionEvent包含3个事件：

- compositionStart：输入开始
- compositionUpdate：输入变化，没有点选中文之前触发多次
- compositionEnd：点选中文，输入结束

我们可以监听compositionStart和compositionEnd事件，设置一个标志位表示用户是否正在输入。如果正在输入，就不判断是否超出限制长度，等输入完毕再根据限制长度截取字符串。

### Example

```js
handleChange = (e, limitNum) => {
  const valueArr = [...event.target.value];
  const isSlice = valueArr.length > limitNum && this.isComposing;
  this.setState({
    text: isSlice ? valueArr.join('') : valueArr.slice(0, limitNum).join('')
  });
}
  
// 监听输入法
onComposition = (e) => {
  if (e.type === 'compositionstart') {
    this.isComposing = true;
  } else if (e.type === 'compositionend') {
    // 停止输入时截取限定长度的字符串
    this.isComposing = false;
    this.setState({
      text: [...this.state.text].slice(0, limitNum).join(''),
    })
  }
}
  
render() {
  const { text } = this.state;
  ...
  return (
    <textarea
      value={text}
      onChange={(e) => this.handleChange(e, limitNum)}
      onCompositionStart={(e) => this.onComposition(e, limitNum)}
      onCompositionEnd={(e) => this.onComposition(e, limitNum))
    />
  )
}
```