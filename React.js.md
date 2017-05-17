# React/JSX 编码规范

## 基本规范

- 每个文件只包含的一个 React 组件：
    - 联系紧密的组件可以使用「命名空间」的形式；
    - 每个文件中可包含多个纯函数组件。
- 始终使用 JSX 语法，不要使用 `React.createElement` 创建 ReactElement，以提高编写速度、可读性、可维护性（没有 JSX 转换的特殊场景例外，如在 `console` 中测试组件）。

## 组件创建方式

React 中可以通过三种方式创建组件：ES6 `class`、[`createReactClass`](https://facebook.github.io/react/docs/react-without-es6.html)、[函数式组件](https://facebook.github.io/react/docs/components-and-props.html#functional-and-class-components) 。

- 如果组件有内部状态，或者使用了生命周期方法，优先使用 `class extends React.Component` ：

    ```js
    // bad
    var createReactClass = require('create-react-class');
    var Greeting = createReactClass({
      /// ...
      render() {
        return <h1>Hello, {this.props.name}</h1>;
      }
    });
    
    // good
    class Greeting extends React.Component {
      // ...
      render() {
        return <h1>Hello, {this.props.name}</h1>;
      }
    }
    ```

    如果组件内部未使用状态或生命周期方法，优先使用普通函数（非箭头函数）创建组件：

    ```js
    // bad
    class Listing extends React.Component {
      render() {
        return <div>{this.props.hello}</div>;
      }
    }

    // bad (relying on function name inference is discouraged)
    const Listing = ({ hello }) => (
      <div>{hello}</div>
    );

    // good
    function Listing({ hello }) {
      return <div>{hello}</div>;
    }
    ```

参考链接：

- [React.createClass versus extends React.Component](https://toddmotto.com/react-create-class-versus-component/)


## Mixins

- [不要使用 mixins](https://facebook.github.io/react/blog/2016/07/13/mixins-considered-harmful.html)；
- 使用 ES6 class 语法的 React 组件不支持 mixins（参考[高阶组件](https://facebook.github.io/react/docs/higher-order-components.html)）。

>   Why? Mixins introduce implicit dependencies, cause name clashes, and cause snowballing complexity. Most use cases for mixins can be accomplished in better ways via components, higher-order components, or utility modules.


## 命名规范

- **扩展名**：使用 `.jsx` 作为 React 组件的扩展名；
- **文件名**：使用**大驼峰命名法（PascalCase）**，如 `MyComponent.jsx`；
- **组件命名**：组件名称和文件名一致，如 `MyComponent.jsx` 里的组件名应该是 `MyComponent`；一个目录的根组件使用 `index.jsx` 命名，以目录名称作为组件名称；

    ```js
    // Use the filename as the component name
	
    // file contents
    class CheckBox extends React.Component {
      // ...
    }

    export default CheckBox;

    // in some other file
    // bad
    import CheckBox from './checkBox';

    // bad
    import CheckBox from './check_box';

    // good
    import CheckBox from './CheckBox';
    
    
    // for root components of a directory,
    // use index.jsx as the filename and use the directory name as the component name
      
    // bad
    import Footer from './Footer/Footer.jsx';
        
    // bad
    import Footer from './Footer/index.jsx';
        
    // good
    import Footer from './Footer';
    ```
- **引用命名**：React 组件使用**大驼峰命名法（PascalCase）**，组件实例使用**小驼峰命名法（camelCase）**；

    ```js
        // bad
    import reservationCard from './ReservationCard';
    
    // good
    import ReservationCard from './ReservationCard';
    
    // bad
    const ReservationItem = <ReservationCard />;
    
    // good
    const reservationItem = <ReservationCard />;
    
    // HTML tag
    const myDivElement = <div className="foo" />;
    ReactDOM.render(myDivElement, mountNode);
    ```
	
- **高阶组件命名**: Use a composite of the higher-order component's name and the passed-in component's name as the `displayName` on the generated component. For example, the higher-order component `withFoo()`, when passed a component `Bar` should produce a component with a `displayName` of `withFoo(Bar)`.

    > Why? A component's `displayName` may be used by developer tools or in error messages, and having a value that clearly expresses this relationship helps people understand what is happening.

    ```js
    // bad
    export default function withFoo(WrappedComponent) {
      return function WithFoo(props) {
        return <WrappedComponent {...props} foo />;
      }
    }

    // good
    export default function withFoo(WrappedComponent) {
      function WithFoo(props) {
        return <WrappedComponent {...props} foo />;
      }

      const wrappedComponentName = WrappedComponent.displayName
        || WrappedComponent.name
        || 'Component';

      WithFoo.displayName = `withFoo(${wrappedComponentName})`;
      return WithFoo;
    }
    ```

### 带命名空间的组件

- 如果一个组件有许多关联子组件，可以以该组件作为命名空间编写、调用子组件。

    ```js
    class Form extends React.Component {  
      // ...
    }
 
    class Row extends React.Component {}
    class Label extends React.Component {}
    class Input extends React.Component {}

    Form.Row = Row;
    Form.Label = Label;
    Form.Input = Input;
    
    export default Form;
    
    // refence Form component
    import Form from './Form';

    const App = (
      <Form>
        <Form.Row>
          <Form.Label />
          <Form.Input />
        </Form.Row>
      </Form>
    );
    ```

## 组件声明

- 不要使用 `displayName` 来命名组件，通过引用来命名。

	```js
	// bad
	export default React.createClass({
	  displayName: 'ReservationCard',
	  // stuff goes here
	});

	// good
  class ReservationCard extends React.Component {
  }

	export default ReservationCard;
	```
	
## 属性

### 属性命名

- React 组件的属性使用**小驼峰命名法**；
- 使用 `className` 代替 `class` 属性；
- 使用 `htmlFor` 代替 `for` 属性；
- 不要把 DOM 组件的属性用作其他用途。
    > Why? People expect props like `style` and `className` to mean one specific thing. Varying this API for a subset of your app makes the code less readable and less maintainable, and may cause bugs.

    ```js
    // bad
    <MyComponent style="fancy" />

    // good
    <MyComponent variant="fancy" />
    ```

**传递给 HTML 的属性：**

- 传递给 HTML 元素的自定义属性，需要添加 `data-` 前缀，React 不会渲染非标准属性；
- [无障碍](http://www.w3.org/WAI/intro/aria)属性 `aria-` 可以正常使用。

### 属性设置

- 在组件行内设置属性（以便 `propTypes` 校验），不要在外部改变属性的值；
- 属性较多使用 `{...this.props}` 语法；
    
    ```js
    const component = <Component />;
    component.props.foo = x; // bad
    component.props.bar = y; // also bad
    
    // good
    const component = <Component foo={x} bar={y} />;
    
    // good
    const props = {};
    props.foo = x;
    props.bar = y;
    const component = <Component {...props} />;
    ```
- 属性值明确为 `true` 时，省略值。

    ```js
    // bad
    <Foo
      hidden={true}
    />

    // good
    <Foo
      hidden
    />
    ```

### 属性对齐方式

- 属性较少时可以行内排列；
- 属性较多时每行一个属性，闭合标签单独成行。

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
  onChange={this.inputHandler.bind(this, 'newDinosaurName')}
 />
 
 // if props fit in one line then keep it on the same line
<Foo bar="bar" />

// children get indented normally
<Foo
  superLongParam="bar"
  anotherSuperLongParam="baz"
>
  <Spazz />
</Foo>


// bad
<Foo
  bar="bar"
  baz="baz" />

// good
<Foo
  bar="bar"
  baz="baz"
/>
```

### 属性空格

- 属性 `=` 前后不要添加空格；
- JSX 中的花括号前后不要添加空格。

```js
// bad
<Foo bar={ baz } foo = "bar" />

// good
<Foo bar={baz} foo="bar" />

// good { left: '20px' } 为一个对象
<Foo style={{ left: '20px' }} />
```

### key

- 避免使用数组的索引作为 `key` 值，优先使用唯一 ID 作为 key 值。 （[参考文章](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)）

```js
// bad
{todos.map((todo, index) =>
  <Todo
    {...todo}
    key={index}
  />
)}

// good
{todos.map(todo => (
  <Todo
    {...todo}
    key={todo.id}
  />
))}
```

同时满足以下条件时可以使用数组索引作为 `key` 值：

- 数组是静态的：不经过计算，也不会改变；
- 数组项没有 id；
- 数组不做排序或者过滤操作。

### `propTypes` 及默认值

- 组件属性都应该在 `propTypes` 中声明类型；
- 始终明确指定非必选属性的默认值。

> Why? propTypes are a form of documentation, and providing defaultProps means the reader of your code doesn’t have to assume as much. In addition, it can mean that your code can omit certain type checks.

```js
// bad
function SFC({ foo, bar, children }) {
  return <div>{foo}{bar}{children}</div>;
}

SFC.propTypes = {
  foo: PropTypes.number.isRequired,
  bar: PropTypes.string,
  children: PropTypes.node,
};

// good
function SFC({ foo, bar, children }) {
  return <div>{foo}{bar}{children}</div>;
}

SFC.propTypes = {
  foo: PropTypes.number.isRequired,
  bar: PropTypes.string,
  children: PropTypes.node,
};

SFC.defaultProps = {
  bar: '',
  children: null,
};
```

### A11Y

- Always include an `alt` prop on `<img>` tags. If the image is presentational, `alt` can be an empty string or the `<img>` must have `role="presentation"`. eslint: [`jsx-a11y/alt-text`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/alt-text.md)

  ```js
  // bad
  <img src="hello.jpg" />

  // good
  <img src="hello.jpg" alt="Me waving hello" />

  // good
  <img src="hello.jpg" alt="" />

  // good
  <img src="hello.jpg" role="presentation" />
  ```

- Do not use words like "image", "photo", or "picture" in `<img>` `alt` props. eslint: [`jsx-a11y/img-redundant-alt`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-redundant-alt.md)

  > Why? Screenreaders already announce `img` elements as images, so there is no need to include this information in the alt text.

  ```js
  // bad
  <img src="hello.jpg" alt="Picture of me waving hello" />

  // good
  <img src="hello.jpg" alt="Me waving hello" />
  ```

- Use only valid, non-abstract [ARIA roles](https://www.w3.org/TR/wai-aria/roles#role_definitions). eslint: [`jsx-a11y/aria-role`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/aria-role.md)

  ```js
  // bad - not an ARIA role
  <div role="datepicker" />

  // bad - abstract ARIA role
  <div role="range" />

  // good
  <div role="button" />
  ```

- Do not use `accessKey` on elements. eslint: [`jsx-a11y/no-access-key`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/no-access-key.md)

> Why? Inconsistencies between keyboard shortcuts and keyboard commands used by people using screenreaders and keyboards complicate accessibility.

```js
// bad
<div accessKey="h" />

// good
<div />
```

## 引号

- JSX 属性使用**双引号** `"`；
- JS 使用**单引号** `'`；

```js
// bad
<Foo bar='bar' />

// good
<Foo bar="bar" />

// bad
<Foo style={{ left: "20px" }} />

// good
<Foo style={{ left: '20px' }} />

// JavaScript Expression
const person = <Person name={window.isLoggedIn ? window.name : ''} />;

// HTML/JSX
const myDivElement = <div className="foo" />;
const app = <Nav color="blue" />;

const content = (
  <Container>
    {window.isLoggedIn ? <Nav /> : <Login />}
  </Container>
);
```

## 条件 JSX

- 简短的输出在行内直接三元运算符；

    ```js
    {this.state.show && 'This is Shown'}
    {this.state.on ? 'On' : 'Off'}
    ```
- 较复杂的结构可以在 `.render()` 方法内定义一个以 `Html` 结尾的变量（命名方式仅供参考）；

    ```js
    let dinosaurHtml = '';
    
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
- 运算逻辑简单的直接使用行内迭代。

    ```js
    return (
      <div>
        {this.props.data.map((data) => {
          return (<Component data={data} key={data.id} />);
        })}
      </div>
    );
    ```
	
## `()` 使用

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
	
	// good
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
	
	// good, when single line
	render() {
	  const body = <div>hello</div>;
  
	  return <MyComponent>{body}</MyComponent>;
	}
	```
	
## 自闭合标签

- 自闭合所有没有子组件的标签；
- 自闭合标签 **`/` 前留一个空格**。

```js
// bad
<Logo></Logo>
<Logo/>

// very bad
<Foo                 />

// bad
<Foo
 />

// good
<Logo />
```

## Ref

- 始终使用 ref 回调。 eslint: [`react/no-string-refs`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-string-refs.md)

    ```js
    // bad
    <Foo
      ref="myRef"
    />
  
    // good
    <Foo
      ref={(ref) => { this.myRef = ref; }}
    />
    ```

## 方法

  - 使用箭头函数遮蔽本地变量。
  
    ```js
    function ItemList(props) {
      return (
        <ul>
          {props.items.map((item, index) => (
            <Item
              key={item.key}
              onClick={() => doSomethingWith(item.name, index)}
            />
          ))}
        </ul>
      );
    }
    ```

  - 在 `constructor` 中绑定 `this`，而不是引用的时候绑定。eslint: [`react/jsx-no-bind`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-no-bind.md)

    > Why? A bind call in the render path creates a brand new function on every single render.

    ```js
    // bad
    class extends React.Component {
      onClickDiv() {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv.bind(this)} />;
      }
    }

    // good
    class extends React.Component {
      constructor(props) {
        super(props);

        this.onClickDiv = this.onClickDiv.bind(this);
      }

      onClickDiv() {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv} />;
      }
    }
    ```

  - 不要使用下划线作为 React 组件方法的前缀。
    > Why? Underscore prefixes are sometimes used as a convention in other languages to denote privacy. But, unlike those languages, there is no native support for privacy in JavaScript, everything is public. Regardless of your intentions, adding underscore prefixes to your properties does not actually make them private, and any property (underscore-prefixed or not) should be treated as being public. See issues [#1024](https://github.com/airbnb/javascript/issues/1024), and [#490](https://github.com/airbnb/javascript/issues/490) for a more in-depth discussion.

    ```js
    // bad
    React.createClass({
      _onClickSubmit() {
        // do stuff
      },

      // other stuff
    });

    // good
    class extends React.Component {
      onClickSubmit() {
        // do stuff
      }

      // other stuff
    }
    ```

  - `render` 方法中应该始终返回值。eslint: [`react/require-render-return`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/require-render-return.md)

    ```js
    // bad
    render() {
      (<div />);
    }

    // good
    render() {
      return (<div />);
    }
    ```
- 事件处理方法以 `handle` 或者 `on` 开头，如 `handleClick() {}`。   
- 慎用 [Class Properties](https://babeljs.io/docs/plugins/transform-class-properties/) 语法（最终规范可能会跟提案有差异）。

    ```js
    class SayHello extends React.Component {
      constructor(props) {
        super(props);
        this.state = {message: 'Hello!'};
      }
      // WARNING: this syntax is experimental!
      // Using an arrow here binds the method:
      handleClick = () => {
        alert(this.state.message);
      }
    
      render() {
        return (
          <button onClick={this.handleClick}>
            Say hello
          </button>
        );
      }
    }
    ```

## 组件代码组织

- 按照生命周期顺序组织组件的属性、方法；
- 方法（属性）之间空一行；
- `render()` 方法始终放在最后；
- 自定义方法 React API 方法之后、`render()` 之前；
- `class extends React.Component` 顺序：

  1. `static` 属性
  1. `static` 方法
  1. `constructor`
  1. `getChildContext`
  1. `componentWillMount`
  1. `componentDidMount`
  1. `componentWillReceiveProps`
  1. `shouldComponentUpdate`
  1. `componentWillUpdate`
  1. `componentDidUpdate`
  1. `componentWillUnmount`
  1. *点击处理函数或者其他事件处理函数*，如 `onClickSubmit()` 或 `onChangeDescription()`
  1. *`render` 的 getter 方法*，如 `getSelectReason()` 或 `getFooterContent()`
  1. *可选的 render 方法*，如 `renderNavigation()` 或 `renderProfilePicture()`
  1. `render`

- 定义 `propTypes`, `defaultProps`, `contextTypes`：

    ```js
    import React from 'react';
    import PropTypes from 'prop-types';

    class Link extends React.Component {
      static methodsAreOk() {
        return true;
      }

      render() {
        return (
          <a href={this.props.url} data-id={this.props.id}>
            {this.props.text}
          </a>
        ); 
      }
    }

    Link.propTypes = {
      id: PropTypes.number.isRequired,
      url: PropTypes.string.isRequired,
      text: PropTypes.string,
    };
  
    Link.defaultProps = {
      text: 'Hello World',
    };

    export default Link;
  
    
    // static
    import React from 'react';
    import PropTypes from 'prop-types';
    
    class Link extends React.Component {
      static propTypes = {
        id: PropTypes.number.isRequired,
        url: PropTypes.string.isRequired,
        text: PropTypes.string,
      };
    
      static defaultProps = {
        text: 'Hello World',
      };
    
      static methodsAreOk() {
        return true;
      }

      render() {
        return (
          <a href={this.props.url} data-id={this.props.id}>
            {this.props.text}
          </a>
        );
      }
    }

    export default Link;
    ```

- `React.createClass` 顺序：eslint: [`react/sort-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/sort-comp.md)

  1. `displayName`
  1. `propTypes`
  1. `contextTypes`
  1. `childContextTypes`
  1. `mixins`
  1. `statics`
  1. `defaultProps`
  1. `getDefaultProps`
  1. `getInitialState`
  1. `getChildContext`
  1. `componentWillMount`
  1. `componentDidMount`
  1. `componentWillReceiveProps`
  1. `shouldComponentUpdate`
  1. `componentWillUpdate`
  1. `componentDidUpdate`
  1. `componentWillUnmount`
  1. *点击处理函数或者其他事件处理函数*，如 `onClickSubmit()` 或 `onChangeDescription()`
  1. *`render` 的 getter 方法*，如 `getSelectReason()` 或 `getFooterContent()`
  1. *可选的 render 方法*，如 `renderNavigation()` 或 `renderProfilePicture()`
  1. `render`

## 注释

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

## `isMounted`

  - Do not use `isMounted`. eslint: [`react/no-is-mounted`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-is-mounted.md)

  > Why? [`isMounted` is an anti-pattern][anti-pattern], is not available when using ES6 classes, and is on its way to being officially deprecated.

  [anti-pattern]: https://facebook.github.io/react/blog/2015/12/16/ismounted-antipattern.html

## 代码校验工具

- [ESLint](https://www.github.com/eslint/eslint)
- [ESLint React Plugin](https://github.com/yannickcr/eslint-plugin-react)
- [ESLint JSX A11Y Plugin](https://github.com/evcohen/eslint-plugin-jsx-a11y)


## 参考资源

- [驼峰命名法](http://zh.wikipedia.org/wiki/%E9%A7%9D%E5%B3%B0%E5%BC%8F%E5%A4%A7%E5%B0%8F%E5%AF%AB)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [Airbnb React/JSX Style Guide](https://github.com/airbnb/javascript/tree/master/react)
- [React 组件规范及生命周期](http://facebook.github.io/react/docs/component-specs.html)
- [React DOM 术语表](http://facebook.github.io/react/docs/glossary.html)
- [React 支持的 HTML 标签和属性](http://facebook.github.io/react/docs/tags-and-attributes.html)
- [React 不自动添加 `px` 的 style 属性](http://facebook.github.io/react/tips/style-props-value-px.html)
