---
title: React的认识与初步尝试
date: 2021-12-19 08:39:01
tags: [React, 学习]
categories:
- React
---

### 一、React是什么

一个JavaScipt库，配合JSX（一个JavaScipt语法扩展）能够更好描述UI应该呈现的交互本质形式。

### 二、初步使用React实例

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title></title>
        <script src="../js/react.development.js" crossorigin></script>
        <script src="../js/react-dom.development.js" crossorigin></script>
        <!-- 上面两个Js文件是有固定的引用先后顺序 -->
        <script src="../js/babel.min.js"></script>
    </head>
    <body>
        <div id="test1"></div>

    </body>
    <script type="text/babel">
        function fun(name){
            return name+' hh';
        }
        class HelloMsg extends React.Component{
            render(){
                return (
                <div>
                  <div>hello React</div>
                  <h3>{this.props.name}</h3>
                  <h2>{2+3}</h2>
                  <h1>{fun('abc')}</h1>
                </div>
                )
            }
        }
        ReactDOM.render(<HelloMsg name="hhh"/>,document.getElementById("test1"))

        </script>
</html>
```

第一步：导入相关js文件

第二步：在body中定义需要渲染的元素

第三步：在js区域自定义组件，并调用`ReactDOM.render`进行元素渲染。

之后仔细分析js部分代码：

```javascript
function fun(name){
            return name+' hh';
    }
class HelloMsg extends React.Component{
    render(){
        return (
            <div>
              <div>hello React</div>
              <h3>{this.props.name}</h3>
              <h2>{2+3}</h2>
              <h1>{fun('abc')}</h1>
            </div>
            )
        }
    }
ReactDOM.render(<HelloMsg name="hhh"/>,document.getElementById("test1"))
```

在方法`ReactDOM.render(element, document.getElementById('root'));`为将自定义的元素`element`覆盖html中id为root的块元素。因为覆盖Html中的元素只有一个，所以返回的也是一个块元素，里面可以包裹很多小元素，在使用html元素时使用js相关只要使用`{}`就可以。而元素传参运用`this.props`来进行传输。

React元素不可修改，更新的方法只能是创建一个新的元素传入ReactDOM.render

React 只更新它需要更新的部分,尽管每一秒我们都会新建一个描述整个 UI 树的元素，React DOM 只会更新实际改变了的内容，也就是例子中的文本节点。

### 三、定义React元素

可以通过以下方法自定义元素。

```javascript
//自定义元素element
const element = (
    <div>
        <h1>h1h1</h1>
        <h2>h2h2</h2>
    </div>
);
ReactDOM.render(element,document.getElementById("test1"))
```

### 四、自定义组件

React可以自定义复杂的组件，有两种自定义组件的方法，分别是函数的方法和类的方法。如下例所示：

```
// 用函数的方式自定义组件
function HelloMsg(props){
    return <h1>hello~ {props.name} !</h1>
}
// ES6的类自定义组件
class HiMsg extends React.Component{
    render(){
            return <h2>hi~ {this.props.name}</h2>
    }    
}
```

其中，函数方法的元素传参方式和类的元素传参方式有所区别。

所有 React 组件都必须像纯函数一样保护它们的 props 不被更改。

自定义组件还可以组合使用，如下所示：

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title></title>
        <script src="../js/react.development.js" crossorigin></script>
        <script src="../js/react-dom.development.js" crossorigin></script>
        <!-- 上面两个Js文件是有固定的引用先后顺序 -->
        <script src="../js/babel.min.js"></script>
    </head>
    <body>
        <div id="test1"></div>

    </body>
    <script type="text/babel">
        // 组合组件

        // 用函数的方式自定义组件
        // 自定义组件可以实现交互传参
        function HelloMsg(props){
            return <h1>hello~ {props.name} !</h1>
        }
        // ES6的类自定义组件
        class HiMsg extends React.Component{
            render(){
                return <h2>hi~ {this.props.name}</h2>
            }

        }

        // 组合组件
        function App(){
            return (
              <div>
                  <HelloMsg name="zhangsan"/>
                  <HiMsg name="zhangsan"/>
                  <HelloMsg name="Mody"/>
                  <HiMsg name="Mody"/>
                  <HelloMsg name="Recy"/>
                  <HiMsg name="Recy"/>
              </div>
            )
        }
        ReactDOM.render(<App/>,document.getElementById("test1"))

        </script>
</html>
```
