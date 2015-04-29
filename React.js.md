# React.js 编码规范

## 基本格式

- 缩进两个空格。
- 使用 JSX，而不是使用 `React.createElement` 创建 ReactElement，以提高编写速度、可读性、可维护性。
- **暂**不使用 ES6。

### 组件内部代码组织

- 按照生命周期组顺序织组件的方法、属性；
- 方法（属性）之间空一格；
- `.render()` 方法始终放在最后；
- 自定义方法 React API 方法之后、`.render()` 之前。

```js
React.createClass({  
  displayName: '',
  
  mixins: [],
  
  propTypes: {},
  
  getDefaultProps: function() {
    // ...
  },
  
  getInitialState: function() {
    // do something
  },
  
  componentWillMount: function() {
  	// do something
  },
  
  componentDidMount: function() {
    // do something: add DOM event listener, etc.
  },
  
  componentWillUnmount: function() {
  	// do something: remove DOM event listener. etc.
  },
  
  handleClick: function() {
    // ...
  },
  
  render: function() {
    // ...
  }
});
```

### 注释

- 组件之间的注释需要用 `{}` 包裹。

	```js
	var content = (
	  <Nav>
	    {/* child comment, put {} around */}
	    <Person
	      /* multi
	         line
	         comment */
	      name={window.isLoggedIn ? window.name : ''} // end of line comment
	    />
	  </Nav>
	);
```


### 引号使用

- JS 表达式是使用 `{}` 包裹，里面使用**单引号**；
- HTML/JSX 设置属性时使用**双引号**。

```js
// JavaScript Expression
var person = <Person name={window.isLoggedIn ? window.name : ''} />;

// HTML/JSX
var myDivElement = <div className="foo" />;
var app = <Nav color="blue" />;
var content = (
  <Container>
    {window.isLoggedIn ? <Nav /> : <Login />}
  </Container>
);
```

### 条件 HTML

- 简短的输出在行内直接三元运算符；

	```js
	{this.state.show && 'This is Shown'}
	{this.state.on ? 'On' : 'Off'}
	```
- 较复杂的结构可以在 `.render()` 方法内定义一个以 `Html` 结尾的变量。

	```js
	var dinosaurHtml = '';
	
	if (this.state.showDinosaurs) {  
	  dinosaurHtml = (
	    <section>
	      <DinosaurTable />
	      <DinosaurPager />
	    </section>
	  );
	}

	return (  
	  <div>
	    ...
	    {dinosaurHtml}
	    ...
	  </div>
	);
	```	
	
### JSX 作为变量或返回值

- 多行的 JSX 使用 `()` 包裹，有组件嵌套时使用多行模式；

	```js
	// bad
	return (<div><ComponentOne /><ComponentTwo /></div>);
	
	// good
	var multilineJsx = (  
	  <header>
	    <Logo />
	    <Nav />
	  </header>
	);
	
	// ...
	return (
      <div>
        <ComponentOne />
        <ComponentTwo />
      </div>
    );
	```
- 单行 JSX 省略 `()`。

	```js
	var singleLineJsx = <h1>Simple JSX</h1>;  
	```	
	
### 自闭合标签

- JSX 中所有标签**必须闭合**；
- 没有子元素的组件使用自闭合语法，自闭合标签 **`/` 前留一个空格**。

```js
// bad
<Logo></Logo>
<Logo/>

// good
<Logo />
```
	

## 命名规范

- ReactComponent 使用**大驼峰**命名法；
- HTML 标签、ReactComponent 实例使用**小驼峰**命名法。

	```js
	// HTML tag
	var myDivElement = <div className="foo" />;
	React.render(myDivElement, mountNode);

	// ReactComponent
	var MyComponent = React.createClass({
	});

	// ReactComponent instance
	var myElement = <MyComponent someProp="true" />;

	React.render(myElement, mountNode);
	```

- 如果模块输出的单一的 class，文件名应该是 class 的名字。

	```js
	// file contents
	var CheckBox = React.createClass({
	  // ...
	})

	module.exports = CheckBox;

	// in some other file
	// bad
	var CheckBox = require('./checkBox');

	// bad
	var CheckBox = require('./check_box');

	// good
	var CheckBox = require('./CheckBox');
	```
	
### 带命名空间的组件

- 如果一个组件有许多关联子组件，可以以该组件作为命名空间编写、调用子组件。

	```js
	var MyFormComponent = React.createClass({ ... });

	MyFormComponent.Row = React.createClass({ ... });
	MyFormComponent.Label = React.createClass({ ... });
	MyFormComponent.Input = React.createClass({ ... });


	var Form = MyFormComponent;

	var App = (
	  <Form>
	    <Form.Row>
	      <Form.Label />
	      <Form.Input />
	    </Form.Row>
	  </Form>
	);
	```
	
### 行内迭代

- 运算逻辑简单的直接使用行内迭代。

```js
return (
  <div>
    {this.props.data.map(function(data, i) {
      return (<Component data={data} key={i} />)
    })}
    </div>
);
```    	
	

## 属性


### 属性命名

- React 组件的属性使用小驼峰命名法；
- 使用 `className` 代替 `class` 属性；
- 使用 `htmlFor` 代替 `for` 属性。

**传递给 HTML 的属性：**

- 传递给 HTML 元素的自定义属性，需要添加 `data-` 前缀，React 不会渲染非标准规范；
- [无障碍](http://www.w3.org/WAI/intro/aria)属性 `aria-` 可以正常使用。

### 属性设置

- 在组件行内设置属性（以便 `propTypes` 校验），不要在外部改变属性的值；
- 属性较多使用 `{...props}` 语法；
- 重复设置属性值时，前面的值会被后面的覆盖。

```js
var component = <Component />;
component.props.foo = x; // bad
component.props.bar = y; // also bad

// good
var component = <Component foo={x} bar={y} />;

// good
var props = {};
props.foo = x;
props.bar = y;
var component = <Component {...props} />;

var props = { foo: 'default' };
var component = <Component {...props} foo={'override'} />;
console.log(component.props.foo); // 'override'
```

### 属性格式

- 属性较少时可以行内排列；
- 属性较多时每行一个属性。

```js
// bad - too long
<input type="text" value={this.state.newDinosaurName} onChange={this.inputHandler.bind(this, 'newDinosaurName')} />  

// bad - aligning attributes after the tag
<input type="text"  
       value={this.state.newDinosaurName}
       onChange={this.inputHandler.bind(this, 'newDinosaurName')} />

// good
<input  
  type="text"
  value={this.state.newDinosaurName}
  onChange={this.inputHandler.bind(this, 'newDinosaurName')} />
```  

## 代码校验工具

- [ESLint](https://www.github.com/eslint/eslint)
- [ESLint React Plugin](https://github.com/yannickcr/eslint-plugin-react)


## 参考资源

- [React DOM 术语表](http://facebook.github.io/react/docs/glossary.html)
- [驼峰命名法](http://zh.wikipedia.org/wiki/%E9%A7%9D%E5%B3%B0%E5%BC%8F%E5%A4%A7%E5%B0%8F%E5%AF%AB)
- [React 组件规范及生命周期](http://facebook.github.io/react/docs/component-specs.html)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [React 支持的 HTML 标签和属性](http://facebook.github.io/react/docs/tags-and-attributes.html)
- [React 不自动添加 `px` 的 style 属性](http://facebook.github.io/react/tips/style-props-value-px.html)
