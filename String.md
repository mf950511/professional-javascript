---
title: "String类型"
date: "2020-10-12"
name: 'francis'
age: '25'
tags: [JavaScript回顾]
categories: JavaScript
---

# String类型

- 字符串由16位码元组成，多数字符都是16位码元对应一个字符，length属性就是表示有多少个16位码元
- charAt()方法返回给定索引位置的字符，参数为索引位置
- javascript采用UCS-2和UTF-16两种Unicode编码混合策略，对于(U+0000~U+FFFF)，这两种编码是一样的
- charCodeAt()可以查看对应索引位置的字符编码，参数为索引位置
- fromCharCode()可以根据给定的UTF-16码元创建字符串，然后将其拼接返回

```js
let message = 'abcde'
console.log(message.charAt(2)) // 'c'
console.log(message.charCodeAt(2)) // 99
// 可以16进制入参
console.log(String.fromCharCode(0x61, 0x62, 0x63, 0x64, 0x65)) // "abcde"
console.log(String.fromCharCode(97, 98, 99, 100, 101)) // "abcde"
```

- 对于在U+0000~U+FFFF范围内的字符，length、charAt()、charCodeAt()、fromCharCode()都可以正常运行
- 当拓展到Unicode增补字符平面就不行了，上面的16位只能标识65536个字符，这些表示基本多语言平面，为了表示更多的字符，采用了每个字符使用另外16位去选择一个增补平面。这种每个字符使用两个16位码元的策略称为代理对
- 当我们对含有代理对编码的字符串就会有问题，如下

```js
let message = 'ab😊de'
console.log(message.length)     // 6
console.log(message.charAt(1))  // b
console.log(message.charAt(2))  // �
console.log(message.charAt(3))  // �
console.log(message.charAt(4))  // d 
console.log(message.charCodeAt(1))  // 98
console.log(message.charCodeAt(2))  // 55357
console.log(message.charCodeAt(3))  // 56842
console.log(message.charCodeAt(4))  // 100


console.log(String.fromCharCode(97, 98, 55357, 56842, 100, 101)) // ab😊de
```

- 这里我们能看到笑脸符明显占了两个字符，然后我们尝试使用fromCharCode()来将代理对进行解析为字符串是没有问题的，这是因为浏览器可以正常解析代理对
- 针对这种情况，我们可以使用codePointAt()来替代charCodeAt()
- codePointAt()使用方法跟charCodeAt()基本一致，也是接受一个表示索引的参数并返回该索引的码点。码点是Unicode中一个字符的完整标识，可能是16位，也有可能32位，codePointAt()可以查看完整的码点

```js
let message = 'ab😊de'
console.log(message.codePointAt(1))  // 98
console.log(message.codePointAt(2))  // 128522
console.log(message.codePointAt(3))  // 56842
console.log(message.codePointAt(4))  // 100
```

- 我们可以通过字符串解析来识别代理对，如下

```js
let message = 'ab😊de'
console.log([...message]) // ["a", "b", "😊", "d", "e"]
```

- 这样就将我们的代理对给完整解析了
- 对应的，我们也有fromCodePoint()来讲对应的码点转为字符串，并拼接返回

```js
// 使用 fromCharCode要有6位数字才可以
console.log(String.fromCharCode(97, 98, 55357, 56842, 100, 101)) // ab😊de
// 使用 fromCodePoint
console.log(String.fromCodePoint(97, 98, 128522, 100, 101)) // ab😊de
```

