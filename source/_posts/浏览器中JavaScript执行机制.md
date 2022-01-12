---
title: 浏览器中JavaScript执行机制
date: 2021-12-27 09:15:42
tags: [浏览器, JavaScript, 前端面试]
categories:
- 浏览器
---

---

*本文来源于[http://interview.poetries.top，然后在阅读过程中发现这是出于极光课程[《浏览器工作原理与实践》_-极客时间](https://time.geekbang.org/column/intro/216)。本文章是在阅读该资料时的笔记整理，有部分直接搬运原文，侵删。](http://interview.poetries.top%EF%BC%8C%E7%84%B6%E5%90%8E%E5%9C%A8%E9%98%85%E8%AF%BB%E8%BF%87%E7%A8%8B%E4%B8%AD%E5%8F%91%E7%8E%B0%E8%BF%99%E6%98%AF%E5%87%BA%E4%BA%8E%E6%9E%81%E5%85%89%E8%AF%BE%E7%A8%8B%5B%E3%80%8A%E6%B5%8F%E8%A7%88%E5%99%A8%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E4%B8%8E%E5%AE%9E%E8%B7%B5%E3%80%8B_-%E6%9E%81%E5%AE%A2%E6%97%B6%E9%97%B4%5D(https://time.geekbang.org/column/intro/216)%E3%80%82%E6%9C%AC%E6%96%87%E7%AB%A0%E6%98%AF%E5%9C%A8%E9%98%85%E8%AF%BB%E8%AF%A5%E8%B5%84%E6%96%99%E6%97%B6%E7%9A%84%E7%AC%94%E8%AE%B0%E6%95%B4%E7%90%86%EF%BC%8C%E6%9C%89%E9%83%A8%E5%88%86%E7%9B%B4%E6%8E%A5%E6%90%AC%E8%BF%90%E5%8E%9F%E6%96%87%EF%BC%8C%E4%BE%B5%E5%88%A0%E3%80%82)*

---

## 1.变量提升

使用过JavaScipt的程序员都清除JavaScipt是按顺序执行的，不过看以下代码的输出结果，就会发现似乎并没有按'顺序'执行。

```
showName()
console.log(myname)
var myname = '极客时间'
function showName() {
    console.log('函数showName被执行');
}
```

执行结果如下图所示：

![](/images/react/2021122701.png)

据图可知，在变量和函数进行声明之前就调用了它们，但是依旧能够执行。并且函数能够正常执行但是变量却是`underfined`。如果删掉变量声明，即上述代码的第三行，那么执行结果又会是什么呢？如下图所示，代码会报错。

![](/images/react/2021122702.png)

可见如果在下文定义了变量和函数，那么在上文中使用该变量和函数时不会报错，不过函数可以顺利输出结果，但是变量却是`underfined`，而如果在下文没有定义，则会报错。这种情况被称为<u>变量提升</u>。

### 1.1 什么是变量提升

首先先要了解<u>声明和赋值</u>的概念，对于下面这行代码：

```
varname = 'name'
```

其实可以分为两个阶段，即变量的声明阶段和变量赋值阶段：

```
var name = underfined
name = 'name'
```

而争对函数可以看下段代码

```
function foo(){
    console.log('foo')
}//这个函数定义本身就是函数声明

var bar=function(){
    console.log('bar')
}
//变量声明 var bar = underfined
//变量赋值 var = function(){ ... }
```

可见对于函数来说，<u>函数声明</u>就是函数定义本身。

变量提升就是在JavaScipt代码执行过程中，把变量和函数的声明部分提到代码开头的行为，对于变量声明会设置默认值即`underfined`。

如下图所示，就是我们的第一段代码进行变量提升后造成的效果。

```
//先进行变量提升，即提取变量和函数的声明
var myname = underfined
function showName(){
    console.log('showName被调用')
}

//再是代码的可执行部分
showName()
console.log(myname)
myname='极客时间'//去掉原来的var变量声明，变成变量赋值操作
```

上面就是变量提升带来的效果，其实一共就是两个步骤：1.提取变量和函数的声明放在代码顶部，对于变量声明进行`underfined`赋值。2.再执行代码可执行部分，对于原先的声明进行去除操作。

### 1.2 Java代码的执行流程

在了解什么是变量声明后，我们会以为变量声明的过程中是将代码的顺序进行改变的过程，但其实并不是，<u>实际上代码的位置并没有改变</u>，变量和函数声明的位置也没有变。JavaScipt的执行流程如下图所示：

![](/images/react/2021122703.png)

其中，变量提升的实现其实是在<u>编译阶段</u>实现的。输入一段代码，经过编译后会生成两部分内容：执行上下文和可执行代码。如下图所示：

![](/images/react/2021122704.png)

据图可知，在执行上下文中会保存代码的<u>环境变量</u>，其中就包括变量声明。

```
showName()
console.log(myname)
var myname = '极客时间'
function showName() {
    console.log('函数showName被执行');
}
```

对于这段代码，编辑阶段的JavaScript引擎会对其进行分析处理，第1行第2行代码因为不是变量？函数声明则不进行处理，第3行是变量声明，于是JavaScipt引擎会在环境对象中创造一个myname的属性并初始化为`underfined`，第4行则是一个函数声明，JavaScript引擎会将函数定义存储岛堆中，并在环境对象中创建一个showName属性，将该属性指向堆中函数的位置。接下来JavaScipt会将声明以外的代码编译成字节码。这样就有了包含环境对象（变量声明）的执行上下文和可执行代码了。

接下来就是进行执行阶段。在执行阶段，JavaScipt引擎是按照可执行代码的顺序一步一步执行的，争对上面这段代码的可执行部分就是：

```
showName()
console.log(myname)
myname='极客时间'
```

执行第1行时，JavaScript引擎会先在执行上下文的变量环境中寻找与此函数同名的属性，如果找到，就输出该属性的引用结果。

执行第2行时，JavaScipt引擎同理在变量环境中查找`myname`属性，找到后就输出该属性值。

执行第3行时，会将变量环境中的`myname`属性的值改为“极客时间”。

以上就是变量提升在JavaScipt实际编译流程中的实现原理。

3.代码中出现相同的变量/函数怎么办

如下列代码所示：

```
function showName() {
    console.log('极客邦');
}
showName();
function showName() {
    console.log('极客时间');
}
showName(); 
```

实际上在编译阶段获取变量环境时就会将已有的showName属性在第一次定义后进行第二次修改，或者是覆盖第一次定义。所以实际运行时两个函数输出的结果是相同的，都是‘极客时间’。

## 2.调用栈

上面一节通过变量提升的概率了解了JavaScipt引擎的编译过程，接下来具体深入该过程，这一节了解的是调用栈这一概念。

### 2.1 什么是函数调用

函数调用就是运行一个函数，如下列代码所示：

```
var a = 2
function add(){
var b = 10
return  a+b
}
add()
```

其中`add()`就是一个函数调用。根据这段代码按照上一节所学的可以知道，再进行add函数调用前会创建<u>执行上下文</u>，其中包括了变量环境，但这就涉及到了全局变量和局部变量的问题，在函数内部定义的变量又是怎么进行执行上下文创建的呢。

这就是一个顺序问题，首先获得这段JavaScript代码，JavaScript引擎会先创建一个<u>全局执行上下文</u>。在全局执行上下文中有变量环境如下所示：

```
VariableEnvironment：
    a -> underfined,
    add -> function: {
    var b=10
    return a+b
}
```

搭建好全局的执行上写文后就进行全局的可执行代码执行，此时调用add函数，又要建立<u>函数内的执行上下文</u>，函数内的环境变量如下所示：

```
VariableEnvironment：
    b -> underfined
}
```

然后再执行函数内的可执行代码。具体的流程如下图所示：

![](/images/react/2021122705.png)

这其中就需要建立两个执行上下文，但JavaScript是怎么不把它们弄混的呢？这就涉及到了<u>栈</u>的概念。栈就是一种<u>先入后出</u>的结构。

### 2.2 什么是JavaScript的调用栈

JavaScript引擎就是利用栈的结构来管理执行上下文的，在执行上下文创建好后会将其压入栈中，这样的栈就叫做调用栈。

看下面这段代码：

```
var a = 2
function add(b,c){
  return b+c
}
function addAll(b,c){
var d = 10
result = add(b,c)
return  a+result+d
}
addAll(3,6)
```

这段代码的执行过程为第一步，创建全局上下文，并将其压入栈底，如下图所示：

![](/images/react/2021122706.png)

然后执行全局代码。

当运行到调用addAll函数时进行第二步，创建该函数的执行上下文，并将其压入栈中，如下图所示：

![](/images/react/2021122707.png)

之后就进入该函数代码的执行阶段。

当执行到add函数的调用语句时就进行到第三步，为其创建执行上下文，并压入栈中，如下图所示：

![](/images/react/2021122708.png)

当ad函数返回时，调用栈会将顶部的执行上下文弹出，然后继续执行，直到返回到只剩下全局执行上下文为止。调用栈是JavaScript引擎追踪函数执行的一个机制，当一次有多个函数被调用时，通过调用栈就能追踪到哪个函数正在被执行以及各函数之间的调用关系。

### 2.3 在开发中使用调用栈

利用浏览器查看调用栈：比如打开Chrome的开发者工具，在Sources中打开页面，在相关的JavaScript上设置断点，并且刷新运行，然后在右侧的call stack中就可以看到调用栈。如下图所示。

![](/images/react/2021122709.png)

在call stack中最底层的(anonymous)就是全局执行上下文。

使用`console.trace()`来输出当前函数的调用关系，如下段代码所示：

```
var a = 2
function add(b,c)
{ console.trace()//输出当前函数的调用关系
  return b+c
}
function addAll(b,c){
var d = 10
result = add(b,c)
return  a+result+d
}
addAll(3,6)
```

上面代码的输出结果如下图所示。

![](/images/react/2021122710.png)

### 2.4 栈溢出

需要知道的是调用栈是有大小的，所以当栈内的执行上下文数量超过一定值时，JavaScript引擎就会报错，这种情况就叫做<u>栈溢出</u>。

如下段代码所示：

```
function division(a,b){
    return division(a,b)
}
console.log(division(1,2))
```

执行后会出下如图所示报错：

![](/images/react/2021122711.png)

解决栈溢出的问题是<u>尽量避免回调函数的方法</u>，可以使用循环语句来代替回调函数。

如下端代码所示：

```
function runStack (n) {
  if (n === 0) return 100;
  return runStack( n- 2);
}
runStack(50000)
//这段代码就会产生栈溢出的问题，可以进行以下优化


// 优化
function runStack(n) {
    while (true) {
        if (n === 0) {
            return 100;
        }

        if (n === 1) { // 防止陷入死循环
            return 200;
        }

        n = n - 2;
    }
}

console.log(runStack(50000));
```

上面这段代码的优化就是使用了while循环来代替了递归函数。

## 3.块级作用域

在上面两节中提到了JavaScript的变量提升现象，这会造成很多误解问题，为了解决这种缺陷ES6引入了<u>let和cons</u>t来解决。let&const使用块级作用域来解决了这个问题。

### 3.1 作用域

作用域就是变量能够访问的范围，JavaScript只有两种作用域：<u>全局作用域、函数作用域</u>。而其他许多语言则还有<u>块级作用域</u>，即用`{}`包围的区间就可以称为一个块级作用域，比如在Java中以下代码都是块级作用域：

```
//if块
if(1){}

//while块
while(1){}

//函数块
function foo(){

//for循环块
for(let i = 0; i<100; i++){}

//单独一个块
{}
```

### 3.2 ES6中的let和const

因为JavaScript的最初设计理念比较简单，所以它存在很多由于没有块级作用域带来的问题，为了解决这些问题，ES6引用了let和const关键字，让其也能像其他语言一样拥有块级作用域。

let和const分别是定义变量和常量的关键字，如下列代码所示，使用这两个关键字就能够使用块级作用域。

```
function letTest() {
  let x = 1;
  if (true) {
    let x = 2;  // 不同的变量
    console.log(x);  // 2
  }
  console.log(x);  // 1
}
```

如果使用var，则两个输出的都是2 。

### 3.3 JavaScript如何支持块级作用域

我们已知JavaScript实现变量提升的原理，那它又是怎么在此基础上又可以实现支持块级作用域的呢？看以下代码来进行解释分析。

```
function foo(){
    var a = 1
    let b = 2
    {
      let b = 3
      var c = 4
      let d = 5
      console.log(a)
      console.log(b)
    }
    console.log(b) 
    console.log(c)
    console.log(d)
}   
foo()
```

当进入这个foo函数时，会创建该函数的执行上下文，首先会将所有的var变量定义存储在执行上下文的变量环境中，同时将属于函数作用域的let or const 变量存储在执行上下文的词法环境中，如图所示。

![](/images/react/2022011001.png)

此时该函数的执行上下文环境已经定义完成，就需要使用JavaScript引擎执行代码，当执行到函数内部的第一个块级作用域`{}`时，foo函数的执行上下文如图所示。

![](/images/react/2022011002.png)

此时var变量a和let变量b已经被赋值。而由于进入了let的作用域，而在词法环境中创建了一个新栈，存储该块级作用域中的变量。

之后当执行到块级作用域中的`console.log(a)`时，JavaScript引擎就会按照以下步骤来搜索变量。

![](/images/react/2022011003.png)

当执行完块级作用域时，词法环境中相应区间就会从栈顶弹出，如图所示。

![](/images/react/2022011004.png)

如下截图所示为改代码的输出结果。

![](/images/react/2022011005.png)

最后因为块级作用域所占内存已经销毁，所以找不到该let变量。

### 3.4 思考题

观察以下代码的输出结果。

```
let myname= '极客时间'
{
  console.log(myname) 
  let myname= '极客邦'
}
```

会出现以下报错。

![](/images/react/2022011006.png)

因为在块级作用域中，从`开始到let myname= '极客邦'`代码之间会形成一个暂时性死区，如果中间去访问变量`myname`，会报初始化之前不能访问`myname`的错误。

## 4.作用域链和闭包

### 4.1 作用域链

当涉及到多个作用域中访问变量的情况就要提到作用域链。像以下代码。

```
function bar() {
    console.log(myName)
}
function foo() {
    var myName = " 极客邦 "
    bar()
}
var myName = " 极客时间 "
foo()
```

该代码在bar函数中会输出myName的值，根据上面几节所学，该代码会生成以下的执行上下文调用栈。

![](/images/react/2022011007.png)

在执行代码阶段，bar函数的执行上下文中没有的变量myName应该在哪里找呢，这里其实每一个执行上下文的环境变量中都有一个称为`outer`的外部引用，当该作用域中没有需要的变量，则会向`outer`指向的作用域中去查找。那么bar函数的`outer`会指向哪里呢？如下图所示为各个执行上下文的变量环境outer的指向。

![](/images/react/2022011008.png)

为什么bar函数的outer指向的不是foo函数而是全局呢？这就涉及到<u>函数声明的位置</u>。函数声明的位置觉得了该函数的outer指向，根据该定理观察该代码，可以看出bar和foo函数的声明位置都是在全局，所以它们的outer指向也都是全局。如果bar函数在foo函数内部定义，则它的outer会指向foo函数。这涉及到了<u>词法作用域</u>。

词法作用域就是指作用域是由代码中函数声明的位置来决定的，所以词法作用域是静态的作用域，通过它就能够预测代码在执行过程中如何查找标识符。

如下图所示。

![](/images/react/2022011009.png)

该图所示的词法作用域链顺序是：foo 函数作用域—>bar 函数作用域—>main 函数作用域—> 全局作用域。

### 4.2 块级作用域中的变量查找

观察以下代码：

```
function bar() {
    var myName = " 极客世界 "
    let test1 = 100
    if (1) {
        let myName = "Chrome 浏览器 "
        console.log(test)
    }
}
function foo() {
    var myName = " 极客邦 "
    let test = 2
    {
        let test = 3
        bar()
    }
}
var myName = " 极客时间 "
let myAge = 10
let test = 1
foo()
```

分析其调用栈结构如下图所示：

![](/images/react/2022011010.png)

图中标出的顺序就是其查找变量的顺序。

### 4.3 闭包

观察以下代码：

```
function foo() {
    var myName = " 极客时间 "
    let test1 = 1
    const test2 = 2
    var innerBar = {
        getName:function(){
            console.log(test1)
            return myName
        },
        setName:function(newName){
            myName = newName
        }
    }
    return innerBar
}
var bar = foo()
bar.setName(" 极客邦 ")
bar.getName()
console.log(bar.getName())
```

其中，当执行代码时，会运行函数foo，也就会生成foo的执行上下文，但是执行完后foo的执行上下文就会在<u>内存中销毁</u>。但是在后面两行中却调用了foo的内部方法setName和getName，而这两个方法则使用了其外部函数foo函数的变量，此时变量myName和test1不会被销毁，而变成了<u>专属</u>`bar.setName`和`bar.getName`方法能使用的变量，这种情况就可以把这些只能够被特定函数访问的变量集合称之为<u>闭包</u>。

![](/images/react/2022011011.png)

那这些闭包是如何使用的呢？如下图所示：

![](/images/react/2022011012.png)

据图可知其执行顺序为setName函数执行上下文->该函数的闭包->全局执行上下文。

也可以利用浏览器中的开发者工具来跟踪其查找变量顺序，如图所示。

![](/images/react/2022011013.png)

### 4.4 闭包回收

如果引用闭包的函数是全局变量，那么该闭包就会一直存在直到页面关闭，如果引用闭包的函数是局部变量，那么该闭包会随着函数的销毁而销毁。所以使用闭包需要注意：<u>当闭包会一直使用则定义其函数为全局变量，否则就定义为局部变量。</u>

### 4.5 思考题

观察下面代码，其输出结果是什么，会形成闭包吗？

```
var bar = {
    myName:"time.geekbang.com",
    printName: function () {
        console.log(myName)
    }    
}
function foo() {
    let myName = " 极客时间 "
    return bar.printName
}
let myName = " 极客邦 "
let _printName = foo()
_printName()
bar.printName()
```

这道题其实是个障眼法，只需要确定好函数调用栈就可以很轻松的解答，调用了foo()后，返回的是bar.printName，后续就跟foo函数没有关系了，所以结果就是调用了两次bar.printName()，根据词法作用域，<u>结果都是“极客邦”，也不会形成闭包</u>。

如下图所示。

![](/images/react/2022011014.png)

（其实我也有点糊涂了。）

## 5.this

### 5.1 JavaScript中的this是什么？

this其实和执行上下文有关，执行上下文一共有三种，分别是<u>全局执行上下文、函数执行上下文和eval执行上下文</u>。每个执行上下文中都包括我们已经知道的变量环节、词法环境以及outer。除此之外，this也包含在执行上下文中。所以根据不同的执行上下文，this也有所不同。

### 5.2 全局执行上下文中的this

直接在浏览器的开发者工具控制台中输入`console.log(this)`，可以看到输出的是window对象，因此可知，<u>全局执行上下文中的this指向window对象</u>，这也是this和作用域链的唯一交点，作用域链最底端包含了window对象。

### 5.3 函数执行上下文中的this

观察以下这段代码：

```
function foo(){
  console.log(this)
}
foo()
```

观察输出结果会发现，这个this依旧是指向window。那么可以改变函数内的this指向吗？可以的，有以下几种方法。

**1.通过函数的call方法设置**

如下列代码所示：

```
let bar = {
  myName : " 极客邦 ",
  test1 : 1
}
function foo(){
  this.myName = " 极客时间 "
}
foo.call(bar)
console.log(bar)
console.log(myName)
```

通过`foo.call(bar)`使得foo函数执行上下文中的this指向了bar。除了<u>call方法</u>，也可以使用<u>bind和apply方法</u>来设置函数执行上下文中的this。

**2.通过对象调用方法设置**

如下代码所示：

```
var myObj = {
  name : " 极客时间 ", 
  showThis: function(){
    console.log(this)
  }
}
myObj.showThis()
```

当执行`myObj.showThis()`时就会使得`showThis`中的`this`指向`myObj`对象。

**3.通过构造函数中设置**

如下代码所示：

```
function CreateObj(){
  this.name = " 极客时间 "
}
var myObj = new CreateObj()
```

使用`new`关键字来使函数中的this指向了新对象本身。

### 5.3 this的涉及缺陷以及应对方案

比方说嵌套函数中的this<u>没有继承关系</u>，如段代码所示：

```
var myObj = {
  name : " 极客时间 ", 
  showThis: function(){
    console.log(this)
    function bar(){console.log(this)}
    bar()
  }
}
myObj.showThis()
```

直觉上我们会以为bar函数内部的this也是指向showThis也就是它的外部函数，但其实它指向的是window，解决方法有两种，第一种是在bar函数外使用`var that=this`，在bar函数内使用that就代指showThis中的this，如下代码所示：

```
var myObj = {
  name : " 极客时间 ", 
  showThis: function(){
    console.log(this)
    var that = this
    function bar(){
      that.name = " 极客邦 "
    }
    bar()
  }
}
myObj.showThis()
console.log(myObj.name)
console.log(window.name)
```

第二个方法是使用ES6中的<u>箭头函数</u>来声明bar函数，如下段代码所示：

```
var myObj = {
  name : " 极客时间 ", 
  showThis: function(){
    console.log(this)
    var bar = ()=>{
      this.name = " 极客邦 "
      console.log(this)
    }
    bar()
  }
}
myObj.showThis()
console.log(myObj.name)
console.log(window.name)
```

因为箭头函数不会创建自身的执行上下文。
