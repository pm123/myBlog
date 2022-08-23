---
title: 记录一次线上问题排查(css相关)
date: 2022-08-23 10:49:48
tags:
---

**故障重现：**

| <img src="image-20220621154826808.png" alt="image-20220621154826808" style="zoom:50%;" /> | <img src="image-20220621154948651.png" alt="image-20220621154948651" style="zoom:30%;" /> |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

```css
.wrapper {
  overflow: auto;
  position: relative;
}
.absolute {
  position: absolute;
  top: -30px;
  left: 50%;
}
```

**解决方法：**~~`overflow: auto;`~~

**原因分析：**

首先，absolute元素是相对于它最近的relative祖先元素进行定位，如果relative元素的overflow 为 auto，那么溢出来的元素会被隐藏；

其次，这里的场景就是要把absolute元素放在超出父元素外的位置，那么就不能使用~~`overflow: auto;`~~。

最后，在实际的场景中，wrapper元素本身就不希望滚动，在排查另外一个滚动条相关的样式问题时，加了这个属性，导致absolute元素看不到。

