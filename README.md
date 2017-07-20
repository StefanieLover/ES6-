## ES6笔记

## 字符串的扩展

1. 字符串的Unicode表示法
	```javascript
	"\u0061"
	  // "a"
	 ```
	 但是，这种表示法只限于码点在\u0000~\uFFFF之间的字符。超出这个范围的字符，必须用两个双字节的形式表示。
	 有了这种表示法之后，JavaScript 共有6种方法可以表示一个字符。

	```javascript
	'\z' === 'z'  // true
	'\172' === 'z' // true
	'\x7A' === 'z' // true
	'\u007A' === 'z' // true
	'\u{7A}' === 'z' // true
	```

2. codePointAt()

	codePointAt方法是测试一个字符由两个字节还是由四个字节组成的最简单方法
	```javascript
	function is32Bit(c) {
	  return c.codePointAt(0) > 0xFFFF;
	}

	is32Bit("𠮷") // true
	is32Bit("a") // false
	```
3. String.fromCodePoint()

	ES5提供String.fromCharCode方法，用于从码点返回对应字符，但是这个方法不能识别32位的UTF-16字符（Unicode编号大于0xFFFF）。

	```javascript
	String.fromCharCode(0x20BB7)
	// "ஷ"
	```
	ES6提供了String.fromCodePoint方法，可以识别大于0xFFFF的字符，弥补了String.fromCharCode方法的不足。在作用上，正好与codePointAt方法相反。

	```javascript
	String.fromCodePoint(0x20BB7)
	// "𠮷"
	```
	注意，fromCodePoint方法定义在String对象上，而codePointAt方法定义在字符串的实例对象上。

4. 字符串的遍历接口
	
	ES6为字符串提供了遍历器接口，使得字符串可以被for...of循环遍历
	```javascript
	for (let codePoint of 'foo') {
	  console.log(codePoint)
	}
	```

	```javascript
	var text = String.fromCodePoint(0x20BB7);
	for (let i = 0; i < text.length; i++) {
	  console.log(text[i]);
	}
	// " "
	// " "

	for (let i of text) {
	  console.log(i);
	}
	// "𠮷"
	```

	上面代码中，字符串text只有一个字符，但是for循环会认为它包含两个字符（都不可打印），而for...of循环会正确识别出这一个字符。

5. at()

	提出字符串实例的at方法，可以识别Unicode编号大于0xFFFF的字符，返回正确的字符

	```javascript
	'abc'.at(0) // "a"
	'𠮷'.at(0) // "𠮷"
	```

7. includes(), startsWith(), endsWith()

	- includes()：返回布尔值，表示是否找到了参数字符串。
	- startsWith()：返回布尔值，表示参数字符串是否在源字符串的头部。
	- endsWith()：返回布尔值，表示参数字符串是否在源字符串的尾部。

	这三个方法都支持第二个参数，表示开始搜索的位置。

	```javascript
	var s = 'Hello world!';

	s.startsWith('world', 6) // true
	s.endsWith('Hello', 5) // true
	s.includes('Hello', 6) // false
	```

	上面代码表示，使用第二个参数n时，endsWith的行为与其他两个方法有所不同。它针对前n个字符，而其他两个方法针对从第n个位置直到字符串结束。

8. repeat()
	
	repeat方法返回一个新字符串，表示将元字符歘重复n次，参数如果是小数，回去取整，如果是负数或者Infinity会报错。如果repeat的参数是字符串，则会先转换成数字。

	```
	'na'.repeat(2.9) // "nana"
	```

9. padStart()，padEnd()

	ES2017 引入了字符串补全长度的功能。如果某个字符串不够指定长度，会在头部或尾部补全。padStart()用于头部补全，padEnd()用于尾部补全。

	```javascript
	'x'.padStart(5, 'ab') // 'ababx'
	'x'.padStart(4, 'ab') // 'abax'

	'x'.padEnd(5, 'ab') // 'xabab'
	'x'.padEnd(4, 'ab') // 'xaba'
	```

10. 模板字符串

	模板字符串（template string）是增强版的字符串，用反引号\（\`\）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。模板字符串中嵌入变量，需要将变量名写在${}之中。大括号内部可以放入任意的JavaScript表达式，可以进行运算，以及引用对象属性。模板字符串甚至还能嵌套。

## 数值的扩展

1. 二进制和八进制的表示法

ES6 提供了二进制和八进制数值的新的写法，分别用前缀0b（或0B）和0o（或0O）表示。如果要将0b和0o前缀的字符串数值转为十进制，要使用Number方法

```
Number('0b111')  // 7
Number('0o10')  // 8
```

2. Number.isFinite(), Number.isNaN()

它们与传统的全局方法isFinite()和isNaN()的区别在于，传统方法先调用Number()将非数值的值转为数值，再进行判断，而这两个新方法只对数值有效，Number.isFinite()对于非数值一律返回false, Number.isNaN()只有对于NaN才返回true，非NaN一律返回false。

3. Number.parseInt(), Number.parseFloat()

ES6 将全局方法parseInt()和parseFloat()，移植到Number对象上面，行为完全保持不变

>>这样做的目的，是逐步减少全局性方法，使得语言逐步模块化。

4. Number.isInteger()

Number.isInteger()用来判断一个值是否为整数。需要注意的是，在 JavaScript 内部，整数和浮点数是同样的储存方法，所以3和3.0被视为同一个值

5. Number.EPSILON

在于为浮点数计算，设置一个误差范围。我们知道浮点数计算是不精确的。但是如果这个误差能够小于Number.EPSILON，我们就可以认为得到了正确结果。

6. 安全整数和Number.isSafeInteger()

JavaScript能够准确表示的整数范围在-2^53到2^53之间（不含两个端点），超过这个范围，无法精确表示这个值。

ES6引入了Number.MAX_SAFE_INTEGER和Number.MIN_SAFE_INTEGER这两个常量，用来表示这个范围的上下限。

Number.isSafeInteger()则是用来判断一个整数是否落在这个范围之内。

7. Math对象的扩展

ES6在Math对象上新增了17个与数学相关的方法。所有这些方法都是静态的，只能在Math对象上调用。对于非数值，这些方法都会先使用Number方法将其转为数值

1. Math.trunc
	Math.trunc方法用于去除一个数的小数部分，返回整数部分。对于非数值，Math.trunc内部使用Number方法将其先转为数值。
	对于空值和无法截取整数的值，返回NaN。
2. Math.sign()
	Math.sign方法用来判断一个数到底是正数、负数、还是零。对于非数值，会先将其转换为数值。它会返回五种值。
	- 参数为正数，返回+1；
	- 参数为负数，返回-1；
	- 参数为0，返回0；
	- 参数为-0，返回-0;
	- 其他值，返回NaN。
3. Math.cbrt() 
	Math.cbrt方法用于计算一个数的立方根。
4. Math.clz32()
	JavaScript的整数使用32位二进制形式表示，Math.clz32方法返回一个数的32位无符号整数形式有多少个前导0
	```
	Math.clz32(0) // 32
	Math.clz32(1) // 31
	Math.clz32(1000) // 22
	Math.clz32(0b01000000000000000000000000000000) // 1
	Math.clz32(0b00100000000000000000000000000000) // 2
	```
	clz32这个函数名就来自”count leading zero bits in 32-bit binary representations of a number“（计算32位整数的前导0）的缩写。
5. Math.imul()
	Math.imul方法返回两个数以32位带符号整数形式相乘的结果，返回的也是一个32位的带符号整数。
6. Math.fround()
	Math.fround()方法返回一个数的单精度浮点数形式
	```
	Math.fround(0)     // 0
	Math.fround(1)     // 1
	Math.fround(1.337) // 1.3370000123977661
	Math.fround(1.5)   // 1.5
	Math.fround(NaN)   // NaN
	```
7. Math.hypot() 
	Math.hypot方法返回所有参数的平方和的平方根。

8. 对数方法
ES6新增了4个对数相关方法
	- Math.expm1()
	- Math.log1p()
	- Math.log10()
	- Math.lgo2()
9. 双曲函数方法
ES6新增了6个双曲函数方法
	Math.sinh(x) 返回x的双曲正弦（hyperbolic sine）
	Math.cosh(x) 返回x的双曲余弦（hyperbolic cosine）
	Math.tanh(x) 返回x的双曲正切（hyperbolic tangent）
	Math.asinh(x) 返回x的反双曲正弦（inverse hyperbolic sine）
	Math.acosh(x) 返回x的反双曲余弦（inverse hyperbolic cosine）
	Math.atanh(x) 返回x的反双曲正切（inverse hyperbolic tangent）
10. 指数运算符
ES2016 新增了一个指数运算符（**）。
```
2 ** 2 // 4
2 ** 3 // 8
```
11. Integer 数据类型

根据国际标准IEEE 754，64位浮点数格式的64个二进制位中，第0位到第51位储存有效数字部分，第52到第62位储存指数部分，第63位是符号位，0表示正数，1表示负数

JavaScript 所有数字都保存成64位浮点数，这决定了整数的精确程度只能到53个二进制位。大于这个范围的整数，JavaScript 是无法精确表示的，这使得 JavaScript 不适合进行科学和金融方面的精确计算。

为了与 Number 类型区别，Integer 类型的数据必须使用后缀n表示。
1n + 2n //3n
二进制、八进制、十六进制的表示法，都要加上后缀n。

typeof运算符对于 Integer 类型的数据返回integer。

运算

	- 相等运算符（==）会改变数据类型，是不允许混合使用。
	```
	0n == 0
	// 报错 TypeError

	0n == false
	// 报错 TypeError
	```
	- Interger 类型不能与 Number 类型进行混合运算。




























