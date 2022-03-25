---
title: React的数据更新和监听事件
date: 2021-12-19 09:19:01
tags: [React, 学习]
categories:
- React
---

### 一、使用state实现更新React组件数据

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
        // 使用state实现更新React组件数据

        // 利用class定义自定义组件
        class Clock extends React.Component{
            //调用父类构造器
            constructor(props) {
                super(props);
                //定义state内的数据
                this.state={data:new Date()};
            }

            //生命周期函数：挂载完成时，每一秒调用tick函数，即每一秒改变stata内data的数据。
            componentDidMount(){
                this.timeID=setInterval(
                ()=>this.tick(),1000
                )
            }
            //生命周期函数：即将卸载时
            componentWillUnmount(){
                clearInterval(this.timeID);
            }

            tick(){
                this.setState({
                    data:new Date()
                });
            }
            render(){
                return (
                    <div>
                        <div>hello!!</div>
                        <h5>It is {this.state.data.toLocaleTimeString()} .</h5>
                    </div>
                )
            }
        }
        ReactDOM.render(<Clock />,document.getElementById("test1"))
    </script>
</html>
```

以上为一个时钟渲染实例，通过state和生命周期的使用来实时完成时钟的运行。

构造函数是唯一可以给 `this.state` 赋值的地方，使用 `setState()`修改state里的数据

出于性能考虑，React 可能会把多个 `setState()` 调用合并成一个调用，因为 `this.props` 和 `this.state` 可能会异步更新，所以你不要依赖他们的值来更新下一个状态。

### 二、监听事件

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
        //监听事件
        //实时切换一个按钮的状态

        //定义一个按钮组件
        class Button1 extends React.Component{
            //因为设计到React.Component里的一些特性，所以需要构造器继承父类构造器
            constructor(props) {
                super(props);
                //state里的数据可以更新修改，相当于Vue里的data
                this.state={
                    isToggleOn:true,
                    data1:'haha',
                }

                //为了在该类的方法中调用this.setState来改变state里的数据来实现实时监听，所以，要在构造器里提前将需要的方法绑定this
                this.handleClick=this.handleClick.bind(this)
            }

            //该方法来实现实时切换
            //若前面没有绑定this，则该方法里的this指向underfined
            //prevState是state原先的状态
            //这里的=>({...})等价于=>{return ({...})}
            handleClick(){
                this.setState(prevState=>({
                    isToggleOn:!prevState.isToggleOn
                }))
            }

            // 下面的方法显示打印出prevState的数据
            // handleClick(){
            //     this.setState(prevState=>{
            //         console.log('prevState',prevState)
            //         return ({
            //             isToggleOn:!prevState.isToggleOn
            //         })
            //     })
            // }

            render(){
                return (
                    <button onClick={this.handleClick}>
                        {this.state.isToggleOn?'ON':'OFF'}
                    </button>
                )
            }
        }

        ReactDOM.render(<Button1 />,document.getElementById("test1"))

        </script>
</html>
```

以上例子实现了实时监控切换一个按钮状态。

在 React 中另一个不同点是你不能通过返回 `false` 的方式阻止默认行为。你必须显式的使用 `preventDefault`。

### 三、条件渲染

可以通过以下方法自定义元素。

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
        //条件渲染

        //用函数定义的方式实现子组件的定义
        function SonDiv(props){
            //若isShow=false,则返回null，即该组件不会渲染，会被隐藏
            if(!props.isShow){
                return null;
            }
            return (
                <div>Son is Show</div>
            )
        }

        //用类的方式实现父组件的定义
        class FatherDiv extends React.Component{
            constructor(props) {
                super(props)
                this.state={isShow:true}
                this.handleToggleChilk=this.handleToggleChilk.bind(this)

            }
            handleToggleChilk(){
                this.setState(prevState=>({
                    isShow:!prevState.isShow
                }))
            }

            render(){
                return (
                    <div>
                        <SonDiv isShow={this.state.isShow} />
                        <button onClick={this.handleToggleChilk}>
                            {this.state.isShow?'hide':'show'}
                        </button>
                    </div>
                )
            }
        }

        ReactDOM.render(<FatherDiv />,document.getElementById("test1"))

        </script>
</html>
```

以上案例实现了一个监控+条件渲染的案例，初始状态为：

![](/images/react/2021121901.png)

点击按钮'hide'，则会隐藏子组件并且按钮内容改为'show'，点击'show'则会回到初始状态即显示子组件。