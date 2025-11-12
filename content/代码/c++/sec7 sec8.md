
---
title: "C++ primer plus函数定义与函数调用"
description: "C++ primer plus 第7,8章节笔记，包括函数定义的基本语法、函数原型声明以及函数调用的实现方法。"
permalink: "/c-language-function-definition-and-call"
tags: [C语言, 函数定义, 函数原型, 函数调用, 编程基础]
aliases: [C语言, 函数定义, 函数原型, 函数调用, 编程基础]
created: "2024-12-28"
modified: "2024-12-28"
published: "2024-12-28"
publish: false
draft: false
enableToc: true
lang: "zh-CN"
---

## 7.1
![image.png](https://cdn.nlark.com/yuque/0/2022/png/2591540/1662432217811-da22aed1-b57e-4504-8e91-5d8be0a1bc77.png#clientId=ua0065f1d-c7ac-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=79&id=u03989908&margin=%5Bobject%20Object%5D&name=image.png&originHeight=79&originWidth=261&originalType=binary&ratio=1&rotation=0&showTitle=false&size=26982&status=done&style=none&taskId=u93a33146-a365-47ca-8d50-67c873e6447&title=&width=261)

### 7.1.1 定义函数
![](https://cdn.nlark.com/yuque/0/2022/jpeg/2591540/1662432420684-67ec8474-a51a-4de5-a786-238579309d8c.jpeg)<br />执行返回语句后结束

### 7.1.2函数原型和函数调用

![image.png](https://cdn.nlark.com/yuque/0/2022/png/2591540/1662432086491-46bcb7a3-5d16-46ca-88a7-b5248bf7138f.png#clientId=ua0065f1d-c7ac-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=139&id=uc0fd4462&margin=%5Bobject%20Object%5D&name=image.png&originHeight=139&originWidth=824&originalType=binary&ratio=1&rotation=0&showTitle=false&size=84562&status=done&style=none&taskId=u4dba4d2e-ee13-4605-8953-dc7ffa24f54&title=&width=824)<br />循环不是首先将所有的分子项相乘，而是首先将1.0与第，个分子项相乘，然后除以第一个分母项。<br />然后下一轮循环乘以第二个分子项，并除以第二个分母项。这样得到的乘积将比先进行乘法运算得到的小。<br />例如，对于(10*9)/(2*1)和(10/2)*(91)，-前者将计算90/2，得到45，后者将计算为5*9，-得到45。这两种方法得到的结果相同，但前者的中间值(90)大于后者。因子越多，-中间值的差别就越大。当数字非常大时，这种交替进行乘除运算的策略可以防止中间结果超出最大的浮点数。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/2591540/1662435299939-6e25f9a2-359a-485c-be44-264cf93b7aeb.png#clientId=u395a3c4e-e28b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=113&id=u62b3a115&margin=%5Bobject%20Object%5D&name=image.png&originHeight=113&originWidth=381&originalType=binary&ratio=1&rotation=0&showTitle=false&size=37183&status=done&style=none&taskId=u11d3611e-cb7a-4bad-8968-3e111de3269&title=&width=381)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/2591540/1663559214689-a2875115-5d82-400d-8fb5-1782ef0ec56c.png#clientId=u2e54aa6e-17c5-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=466&id=u8db00ea7&margin=%5Bobject%20Object%5D&name=image.png&originHeight=466&originWidth=718&originalType=binary&ratio=1&rotation=0&showTitle=false&size=349805&status=done&style=none&taskId=u00bc3467-0ad2-4e00-8d5c-9cc0724d855&title=&width=718)![image.png](https://cdn.nlark.com/yuque/0/2022/png/2591540/1663559199961-db424013-7dfb-4a40-9ce6-69604ac0b895.png#clientId=u2e54aa6e-17c5-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=397&id=u2d197bc5&margin=%5Bobject%20Object%5D&name=image.png&originHeight=397&originWidth=580&originalType=binary&ratio=1&rotation=0&showTitle=false&size=299765&status=done&style=none&taskId=u6157ca9e-7c36-4be4-87d2-ae63a19525c&title=&width=580)

