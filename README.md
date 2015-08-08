# React-demo

React是Facebook开源的JavaScript库，用于构建UI。在React里可以传递多种类型的参数，如声明代码，帮助渲染出UI、也可以是静态的HTML DOM元素、也可以传递动态变量、甚至是可交互的应用组件。

demo1：HTML模板和React.render()

<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
	<title>React demo1</title>
<script type="text/javascript" src="../lib/JSXTransformer.js"></script>
<script type="text/javascript" src="../lib/react.js"></script>
</head>
<body>
<div id="example"></div>

<script type="text/jsx">

React.render(
	<h1>Hello, world!</h1>,
	document.getElementById('example')
);	

</script>
</body>
</html>

1.最后一个script标签的type属性为text/jsx。这是React独有的JSX语法,跟JavaScript不兼容。凡是使用JSX的地方,都要加上type="text/jsx".

2.React提供两个库：react.js和JSXTransformer.js，它们必须首先加载。其中，JSXTransformer.js的作用将JSX语法 转化为JavaScript语法。

3.React.render是React的最基本方法，用于将模板转为HTML语言，并插入指定的DOM节点。