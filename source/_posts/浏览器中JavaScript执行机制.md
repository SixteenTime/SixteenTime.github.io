---
title: 浏览器中JavaScript执行机制
date: 2021-12-27 09:15:42
tags: [浏览器, JavaScript, 前端面试]
categories:
- 浏览器
---

1.变量提升

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

可见如果在下文定义了变量和函数，那么在上文中使用该变量和函数时不会报错，不过函数可以顺利输出结果，但是变量却是‘underfined’，而如果在下文没有定义，则会报错。这种情况被称为变量提升。

1.1 什么是变量提升

首先先要了解声明和赋值的概念，对于下面这行代码：

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

可见对于函数来说，函数声明就是函数定义本身。

变量提升就是在JavaScipt代码执行过程中，把变量和函数的声明部分提到代码开头的行为，对于变量声明会设置默认值即underfined。

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

1.2 Java代码的执行流程

在了解什么是变量声明后，我们会以为变量声明的过程中是将代码的顺序进行改变的过程，但其实并不是，实际上代码的位置并没有改变，变量和函数声明的位置也没有变。JavaScipt的执行流程如下图所示：

![](/images/react/2021122703.png)

其中，变量提升的实现其实是在编译阶段实现的。输入一段代码，经过编译后会生成两部分内容：执行上下文和可执行代码。如下图所示：

![](/images/react/2021122704.png)

据图可知，在执行上下文中会保存代码的环境变量，其中就包括变量声明。

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

2.调用栈

上面一节通过变量提升的概率了解了JavaScipt引擎的编译过程，接下来具体深入该过程，这一节了解的是调用栈这一概念。

2.1 什么是函数调用

函数调用就是运行一个函数，如下列代码所示：

```
var a = 2
function add(){
var b = 10
return  a+b
}
add()
```

其中`add()`就是一个函数调用。根据这段代码按照上一节所学的可以知道，再进行add函数调用前会创建执行上下文，其中包括了变量环境，但这就涉及到了全局变量和局部变量的问题，在函数内部定义的变量又是怎么进行执行上下文创建的呢。

这就是一个顺序问题，首先获得这段JavaScript代码，JavaScript引擎会先创建一个全局执行上下文。在全局执行上下文中有变量环境如下所示：

```
VariableEnvironment：
    a -> underfined,
    add -> function: {
    var b=10
    return a+b
}
```

搭建好全局的执行上写文后就进行全局的可执行代码执行，此时调用add函数，又要建立函数内的执行上下文，函数内的环境变量如下所示：

```
VariableEnvironment：
    b -> underfined
}
```

然后再执行函数内的可执行代码。具体的流程如下图所示：

![](/images/react/2021122705.png)

这其中就需要建立两个执行上下文，但JavaScript是怎么不把它们弄混的呢？这就涉及到了栈的概念。栈就是一种先入后出的结构。

2.2 什么是JavaScript的调用栈

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

这段代码的执行过程为

第一步，创建全局上下文，并将其压入栈底，如下图所示：

![](/images/react/2021122706.png)

然后执行全局代码。

当运行到调用addAll函数时进行第二步，创建该函数的执行上下文，并将其压入栈中，如下图所示：

![](/images/react/2021122707.png)

之后就进入该函数代码的执行阶段。

当执行到add函数的调用语句时就进行到第三步，为其创建执行上下文，并压入栈中，如下图所示：

![](/images/react/2021122708.png)

当ad函数返回时，调用栈会将顶部的执行上下文弹出，然后继续执行，直到返回到只剩下全局执行上下文为止。调用栈是JavaScript引擎追踪函数执行的一个机制，当一次有多个函数被调用时，通过调用栈就能追踪到哪个函数正在被执行以及各函数之间的调用关系。

2.3 在开发中使用调用栈

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

2.3 栈溢出

需要知道的是调用栈是有大小的，所以当栈内的执行上下文数量超过一定值时，JavaScript引擎就会报错，这种情况就叫做栈溢出。

如下段代码所示：

```
function division(a,b){
    return division(a,b)
}
console.log(division(1,2))
```

执行后会出下如图所示报错：

![](/images/react/2021122711.png)

解决栈溢出的问题是尽量避免回调函数的方法，可以使用循环语句来代替回调函数。

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
