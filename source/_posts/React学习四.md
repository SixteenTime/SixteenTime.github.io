---
title: React的状态提升&组合VS继承
date: 2021-12-19 13:49:01
tags: [React, 学习]
categories:
- React
---

### 一、状态提升

通常，多个组件需要反应同一个数据变化时，建议使用状态提升，即共享状态提升到最近的父组件中

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<script src="../js/react.development.js" crossorigin></script>
		<script src="../js/react-dom.development.js" crossorigin></script>
		<script src="../js/babel.min.js"></script>
	</head>
	<body>
		<div id="test1"></div>
		
	</body>
	<script type="text/babel">
		//通过map的方式渲染列表
		
		function List(props){
			//获取列表参数
			const numbers=props.numbers;
			//利用map进行渲染
			const listItem=numbers.map((number=>
			<li>{number}</li>));
			
			return (
			  <ul>{listItem}</ul>
			)
		}
		
		const numbers=[1,2,3,4,5];
		ReactDOM.render(
		<List numbers={numbers} />,
		document.getElementById('test1'))

		</script>
</html>

```

运行以上代码，发现已成功渲染列表，但是在控制台获得警告如下：

![](/images/react/2021121903.png)

提示对于列表的每一个元素都需要key关键词，给每一列添加key:`<li key={number.toString()}>{number}</li>));`，key作为列表中每个元素的唯一标识符，是有优化性能的作用。对于应该在哪里设置元素的key，一个好的经验法则是：在 `map()` 方法中的元素需要设置 key 属性。

以上情况可以也通过以下方法实现

```jsx
funtion ListItem(props){
			return <li>{props.value}</li>
}
		
function List2(props){
	const numbers=props.numbers;
	return (
		 <ul>
			{numbers.map((number=>
			<ListItem key={number.toString()} value={number} />
			))}
		</ul>
	)
}
```



### 二、表单

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<script src="../js/react.development.js" crossorigin></script>
		<script src="../js/react-dom.development.js" crossorigin></script>
		<script src="../js/babel.min.js"></script>
	</head>
	<body>
		<div id="test1"></div>
		
	</body>
	<script type="text/babel">
		//表单的运用
		
		//通过类自定义的内有方法来更方便迅捷地操作表单
		
		//自定义表单组件
		class NameForm extends React.Component{
			constructor(props){
				super(props);
				//定义可更改的变量，即表单输入元素
				this.state={value:''};
				//在具体方法中绑定this来实现对state的改变。
				this.handleChange=this.handleChange.bind(this);
				this.handleSubmit=this.handleSubmit.bind(this);
			}
			
			handleChange(event){
				this.setState({value:event.target.value});
			}
			handleSubmit(event){
				//提交前显示名字
				alert('提交的名字：'+this.state.value);
				event.preventDefault();
			}
			render(){
				return(
				  <form onSubmit={this.handleSubmit}>
				    <label>
					  名字:
					  <input type="text" value={this.state.value} onChange={this.handleChange} />
					 </label>
					  <button type="submit">提交</button>
					</form>  
				)
			}
		}
		ReactDOM.render(<NameForm />,document.getElementById('test1'))

		</script>
</html>

```

以上例子实现了一个自定义表单的应用。

注意：在Html中`<textarea>` 组件直接通过子元素定义其文本，而在React中通过属性value来确定其文本。针对`<select>`组件React 并不会使用 `selected` 属性，而是在根 `select` 标签上使用 `value` 属性。这在受控组件中更便捷，因为您只需要在根标签中更新它。

具体关于textarea 和select在React里的具体操作如下所示：

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<script src="../js/react.development.js" crossorigin></script>
		<script src="../js/react-dom.development.js" crossorigin></script>
		<script src="../js/babel.min.js"></script>
	</head>
	<body>
		<div id="test1"></div>
		
	</body>
	<script type="text/babel">
		//表单的运用
		
		//通过类自定义的内有方法来更方便迅捷地操作表单
		
		//自定义表单组件
		class NameForm extends React.Component{
			constructor(props){
				super(props);
				//定义可更改的变量，即表单输入元素
				this.state={
					value:'',
					textValue:'',
					selected:'aa',
				};
				//在具体方法中绑定this来实现对state的改变。
				this.handleChange=this.handleChange.bind(this);
				this.handleSubmit=this.handleSubmit.bind(this);
				this.handleChangeSelect=this.handleChangeSelect.bind(this);
				this.handleChangeTextarea=this.handleChangeTextarea.bind(this);
			}
			
			handleChange(event){
				this.setState({value:event.target.value});
			}
			handleSubmit(event){
				//提交前显示名字
				alert('提交的名字：'+this.state.value);
				event.preventDefault();
			}
			handleChangeTextarea(event){
				this.setState({textValue:event.target.value})
			}
			handleChangeSelect(event){
				console.log('event',event)
				this.setState({selected:event.target.value});
			}
			render(){
				return(
				  <form onSubmit={this.handleSubmit}>
				    <label>
					  名字:
					  <input type="text" value={this.state.value} onChange={this.handleChange} />
					 </label>
					 <label>
						 内容：
						 <textarea  value={this.state.textValue} onChange={this.handleChangeTextarea}/>
					 </label>
					 <label>
					 	选择：
					 	<select value={this.state.selected} onChange={this.handleChangeSelect} >
					 	    <option value='aa'>aa</ option>
					 		<option value='bb'>bb</ option>
					 		<option value='cc'>cc</ option>
					 	</select>
					 </label>
					  <button type="submit">提交</button>
					</form>  
				)
			}
		}
		ReactDOM.render(<NameForm />,document.getElementById('test1'))

		</script>
</html>

```

以上的形式在一个表单中调用了多个onChange，也可以只调用同一个onChange函数同时监听并改变多个数据，可以给每个元素添加 `name` 属性，并让处理函数根据 `event.target.name` 的值选择要执行的操作。具体代码如下所示：

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<script src="../js/react.development.js" crossorigin></script>
		<script src="../js/react-dom.development.js" crossorigin></script>
		<script src="../js/babel.min.js"></script>
	</head>
	<body>
		<div id="test1"></div>
		
	</body>
	<script type="text/babel">
		//表单的运用
		
		//通过类自定义的内有方法来更方便迅捷地操作表单
		
		//自定义表单组件
		class NameForm extends React.Component{
			constructor(props){
				super(props);
				//定义可更改的变量，即表单输入元素
				this.state={
					nameValue:'',
					textValue:'',
					selected:'aa',
				};
				//在具体方法中绑定this来实现对state的改变。
				this.handleChange=this.handleChange.bind(this);
				this.handleSubmit=this.handleSubmit.bind(this);
			}
			
			handleChange(event){
				const value=event.target.value;
				const name=event.target.name;
				this.setState({
					[name]:value
				})
			}
			handleSubmit(event){
				//提交前显示名字
				alert('提交的名字：'+this.state.value);
				console.log('state',this.state)
				event.preventDefault();
			}
			render(){
				return(
				  <form onSubmit={this.handleSubmit}>
				    <label>
					  名字:
					  <input name="nameValue" type="text" value={this.state.value} onChange={this.handleChange} />
					 </label>
					 <label>
						 内容：
						 <textarea name="textValue"  value={this.state.textValue} onChange={this.handleChange}/>
					 </label>
					 <label>
					 	选择：
					 	<select name="selected" value={this.state.selected} onChange={this.handleChange} >
					 	    <option value='aa'>aa</ option>
					 		<option value='bb'>bb</ option>
					 		<option value='cc'>cc</ option>
					 	</select>
					 </label>
					  <button type="submit">提交</button>
					</form>  
				)
			}
		}
		ReactDOM.render(<NameForm />,document.getElementById('test1'))

		</script>
</html>

```

