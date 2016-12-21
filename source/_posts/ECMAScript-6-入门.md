---
title: ECMAScript 6 入门
date: 2016-12-20 17:27:18
tags:
- ES6
- JavaScript
---
## 简介

> harmony

node --harmony  选项打开所有已部署的ES6功能

> Traceur编译器

Node.js用法

npm命令行用法

直接插入网页

在线转换(转为ES5代码)

## let和const命令

> let命令

let声明的变量仅在块级作用域内有效，不会“变量提升”
这样使得立即执行函数(IIFE)不在必要

> const命令

与let相同，仅在自己所在的块级作用域有效

## 变量的解构赋值

> 定义及特性

只能用于数组和对象，可以设置默认值

```javascript
var [foo,[[bar],baz]] = [1,[[2],3]]
let {foo,bar} = {foo:"aaa",bar:"bbb"}
//默认值
var [foo = true] = []
const {x=3} = {}
```

> 用途

1.交换变量值	`[x,y] = [y,x]`
2.从函数返回多个值 `var [a,b,c] = f()`
3.函数参数的定义
```javascript
function f({x,y,z}){
  //todo...
}
f({x:1,y:2,z:3})	//在提取JSON中的数据时实用
```
4.函数参数的默认值
5.遍历Map结构
```javascript
var map = new Map()
map.set('first','hello')
map.set('second','world')

for(let [key,value] of map){
  console.log(key + " is " + value)
}
```
6.指定获取模块的方法
```javascript
const {methodA,methodB} = require('moduleA')
```

## 字符串的扩展
>新增方法

`s.contains(str)` 是否含有字符串str
`s.startWith(str)`以str开头么？
`s.endsWith(str)`以str结尾么？
都支持第二个参数，表示开始搜索的位置
`s.repeat(n)`返回一个新字符串，将s重复n次
> 正则表达式y修饰符

类似于g修饰符，但是y表示从剩余的第一个位置开始。相当于隐含头部标志`^`
```javascript
var s = 'aaa_aa_a'
var r1 = /a+/g
var r2 = /a+/y

r1.exec(s) //['aaa']
r2.exec(s) //['aaa']

r1.exec(s) //['aa']
r2.exec(s) //null
```
> 模版字符串

通过 ` 在字符串中带入变量

```javascript
var name = "bob"
console.log(`Hello ${name}`)
```
## 数值的扩展
> 注意

整数和浮点数使用同样储存方法，所以3和3.0视为同一值

## 数组的扩展
> Array.from()

可将 `类数组` 和 `可遍历对象(包括Set\Map)`  转为数组
> Array.of()

将一组值转为数组 	`Array.of(1,2,3)`
> **arr.find(fn)和arr.findIndex(fn)**

参数是一个回调函数，所有函数依次遍历这个函数，直到第一个返回值是true的元素
```javascript
[1,2,5,10,15].find(function (value,index,arr){
  return value>9
}) //返回10
```
findIndex 返回元素的位置
> arr.keys()   arr.entries()   key.values()

返回一个遍历器，可用于for...of 循环
> 数组推导

```javascript
var arr = [for(a of [1,2,3]) a*2 ]
// arr => [2,4,6]

var a1 = [for(n of [2,5,8]) if (n>2) if(n<8) n]
// a1 => [5]
```
数组推导可以替代map和filter方法
一个数组推导中可用多个for...of结构，构成多重循环
```javascript
[for (s of ['a','b']) for (w of ['c','d') console.log(s + w)]
//ac
//ad
//bc
//bd
```
字符串也可视为数组
```javascript
[for (c of 'abcde') if (/[aeiou]/.test(c)) c].join('')
//ae
```

## 对象的扩展
> 对象复制 Object.assign(o1,o2,...)

第一个参数是目标对象，其后都是源对象
> 增强对象写法

```javascript
var Person = {
  name:'张三',
  birth,	//变量
  hello(){console.log('我的名字：',this.name)} //函数
}
```
> **属性名表达式**

```javascript
var lastWord = 'last word'
var a = {
  'first word':'hello',
  [lastWord]:'world'
}
a[lastWord] == a['last word']
```
> 新原始数据类型 Symbol

原始类型，不能用new生成；每一个Symbol都是不相等的；Symbol适合做标识符，用于对象属性名，保证属性名之间不会冲突
```javascript
var mySymbol = Symbol('Test')
mySymbol.name //Test
```
> Proxy 对象

拦截对目标对象的访问
```javascript
var person = {
  name:"张三"
}
//两个参数：目标对象，要访问的属性
var proxy = new Proxy(person,{
    get:function(target,property){
		if(property in target){
  			return target[property]
		}else{
  			throw new ReferenceError('Property '+ property +"does not exist.")
		}
	}
})

proxy.age //抛出错误
```

## 函数的扩展
> 参数默认值

```javascript
function throwIfMissing(){
	throw new Error('Paras missing')
}
//默认值
function foo(p1 = throwIfMissing()){
	return p1
}

foo()//抛出错误
```
> rest参数(...参数名)

rest参数搭配的变量是数组。
可以不再使用arguments。
其后不能再有其他参数。
```javascript
function add(...values){
	let sum = 0
	for(var val of values){
		sum += val
	}
	return sum
}

add(2,5,3)	//10
```
> 扩展运算符(...)

相当于rest的逆运算
```javascript
Math.max(...[14,3,77])
```
> **箭头函数**（=>）

```javascript
var sum = (n1,n2) => {return n1+n2}
```
注意
1.不可以作为构造函数，内部不能使用arguments
**2.函数内部的this是绑定定义时所在的对象**
```javascript
var handler = {
	id:'001',
  
	init:function(){
  		document.addEventListener('click',
                                 event => this.doSomething(event.type),false)
	}//this绑定了handler对象
  
  	//init: function () {
    //    document.addEventListener('click',function(e){
    //        console.log(this)
    //    },false)
    //},
    //此时找不到this.doSomething  this-->document
  
  	doSomething:function(type){
  		console.log('Handling '+ type + ' for ' + this.id)
	}
}

handler.init() //Handling click for 001
```

## Set和Map数据结构
> Set

不会发生转型:5和'5'是两个不同值。
不能有相同值出现，无顺序。
属性：size  和  constructor
方法：add	delete	has    clear
Array.from()可将Set结构转为数组

```javascript
var arr = Array.from(new Set([1,2,3,4,5,5]))
console.log(arr)
//得到的arr可以去除重复元素
```
> Map

类似于ES的k-v键值对。不同：可以用**对象(包括数组或是DOM对象)**作为键。
可以接受一个数组初始化
```javascript
var map = new Map([['name','张三'],['title','Author']])
```
属性：size
方法：set(key,value)	get(key)	  has(key)	delete(key)	clear()
**注意：只有对同一个对象的引用，Map才视为同一个键**
```javascript
var map = new Map()

map.set(['a'],555)
map.get(['a']) //undefined
```
遍历器：key()		value()	  entries()
遍历器的结果可以用for...of输出
`forEach`方法：
```javascript
map.forEach(function(value,key,map){
	console.log("Key: %s , Value: %s",key,value)
})
```

## Iterator和for...of循环
>  遍历器

Iterator是一种协议，ES6中只要符合此协议便可以用for...of循环，普通对象不能使用
ES6中，对象具有`next`方法就具有遍历器功能，其返回包含`value`和`done`两个属性的对象
> for...of循环

**与for...in不同：**
**1.for...of不能遍历没有Iterator协议的普通对象**
**2.遍历数组时,for...of遍历值**
**3.for...of遍历map结构时，可以同时访问key和vlaue**
**4.for...of 可遍历的范围：数组、类数组、对象、Set和Map，以及字符串**

## Generator函数
> 定义

控制内部状态变化，依次遍历这些状态。
格式：
1.function关键字后面有星号
2.使用yield定义遍历器的每个成员，即不同的内部状态
**Generator本质：**
**提供一种可暂停执行的函数，yield就是暂停标志。next方法遇到yield就会暂停执行，并返回yield后的表达式值作为返回对象的value值。再次调用就继续执行，直到函数执行完毕。**
注意：
1.yield与return类似，只是具备了记录暂停执行位置的功能
2.Generator可以不用yield语句，此时是一个暂缓执行函数
> next方法的参数

next方法的参数，会作为上一条yield语句的返回值带入到函数中，继续执行。
```javascript
function* f(){
	for(var i=0,true;i++){
		var reset = yield i
        if(reset) {i = -1}
	}
}

var g = f()
g.next()//{value:0,done:false}
g.next()//{value:1,done:false}
g.next(true)//true被赋值给reset {value:0,done:false}
```
> 异步操作的应用

将异步操作写在yield里，避免回调函数的写法。
```javascript
//例1
function* loadUI(){
	showLoadingScreen()
    yield loadUIDataAsynchronously()
    hideLoadingScreen()
}

//例2 将耗时操作分成N步
function* longRunTask(){
	yield step1()
    yield step2()
    //...
    yield stepN()
}

scheduler(longRunTask())

function scheduler(task){
	setTimeout(function(){
		if(!task.next().done){
  			scheduler(task) //进行下一步
		}
	},10)
}

//例3  逐行读取文件
function* numbers(){
	let file = new FileReader()
    try{
  		while(!file.eof){
  			yield parseInt(file.readLine(),10)
		}
	}finally{
  		file.close()
	}
}
```
> 结合for...of循环

for...of循环可以自动遍历Generator函数，不需要next
```javascript
//斐波那契数列
function* fibonacci(){
	let [prev,curr] = [0,1]
    for(;;){
  		[prev,curr] = [curr,prev+curr]
        yield curr
	}
}

for(n of fibonacci()){
	if(n>1000) break
    console.log(n)
}
```
> yield* 语句

```javascript
let delegatedIterator = (function* (){
	yield 'Hello!'
    yield 'Bye!'
}())

let delegatingIterator = (function*(){
	yield 'Greeting!'
    yield* delegatedIterator()
    yield 'Ok,bye!'
}())

for(let value of delegatingIterator()){
	console.log(value)
}

//例2：遍历完全二叉树
function Tree(left,label,right){
	this.left = left
    this.label = label
    this.right = right
}

function* inorder(t){
	if(t){
  		yield* inorder(t.left)
        yield t.label
        yield* inorder(t.right)
	}
}
//生成二叉树
function make(array){
  	if(array.length == 1) return new Tree(null,array[0],null)
    return new Tree(make(array[0]),array[1],make(array[2]))
}
let tree = make([[['a'],'b',['c']],'d',[['e'],'f',['g']]])

//遍历二叉树
var result = []
for(let node of inorder(tree)){
  	result.push(node)
}

//['a','b','c','d','e','f','g']
```

## Promise对象
> 基本用法

将异步操作以同步调用的流程表达出来，避免层层嵌套。便于管理和扩展。
**ES6中Promise对象是一个构造函数，接受一个函数作为参数，这个函数的参数是resolve和reject。构造函数返回promise实例，实例具有then方法。then方法接受两个函数作为参数，分别是成功或失败调用。**
注:then方法的成功调用函数中可以再返回promise对象，此时下一个then方法的成功回调函数参数是这个promise对象的运行结果。
>catch方法：捕捉错误

实质是`.then(null,rejection)`别名
Promise对象的错误具有**冒泡性质**，会一直向后传递，直到被捕获。
> Promise.all 方法

参数是promise对象组成的数组，返回一个新promise对象，数组中只要有一个失败，新promise的状态就会变成rejected.
> Promise.resolve方法

将现有的对象(一般是thenable对象)转换成新的ES6的Promise对象

```javascript
var jsPromise = Promise.resolve($.ajax('/whatever.json'))
```
> async函数(ES7标准)

取代回调函数的又一种形式
函数名前加 `async`，表明内部有异步操作。
异步操作应该返回一个promise对象，前面用`await`关键字注明
函数被调用时，遇到await先暂停执行，等待异步操作完成再调用其后的语句
```javascript
function timeout(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}

async function asyncPrint(value, ms) {
  await timeout(ms);
  console.log(value)
}

asyncPrint('hello world', 50);
```
[更多信息](http://www.ruanyifeng.com/blog/2015/05/async.html)

## Class和Module
> Class

通过`Class`可引入类，通过`extend`可以继承类

```javascript
Class Point{
  	//构造函数
 	constructor(x,y){
  		this.x = x
        this.y = y
	}
  	//方法
	toString(){
  		return '('+this.x+','+this.y+')'
	}
}

Class ColorPoint extend Point{
	constructor(x,y,color){
  		super(x,y)	//等同于super.constructor(x,y)
        this.color = color
	}
  
  	toString(){
  		return this.color + ' ' + super()	//此时是对父类求值，调用toString()
	}
}
```

> Module
[更多信息](http://es6.ruanyifeng.com/#docs/module)

## 其他
1.[ES6编程风格](http://wiki.jikexueyuan.com/project/es6/style.html)
2.[ES6开源教程--阮一峰](http://es6.ruanyifeng.com/)




