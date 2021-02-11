[SRC](https://www.toptal.com/react/interview-questions)

The Virtual DOM is an interesting concept; it’s a complex idea that boils down into a much simpler algorithm.

In React, if we create simple ES6 class and print it out, we have a function (as all functions can be used as a constructor in JavaScript):

```js
const app = () => {
    let React = react,
    {Component} = React,
    DOM = reactDom

    class Comments extends Component {
        constructor(props){ super(props) }
        render(){ return <div>test</div> }
    }

    console.log(Comments)

}

require('react', 'react-dom').then(app)
```
The `console.log(Comments)` gives us something that looks like this (after compiled by Babel from ES6 to ES5):

```js
function Comments(props) {
    \_classCallCheck(this, Comments);

    return _possibleConstructorReturn(this, Object.getPrototypeOf(Comments).call(this, props));

}
```
When we write something to draw a React Component to the screen, we might have something like the following:

`DOM.render(<Comments />, document.body)`
The JSX gets transpiled into ES5 by Babel as well:

`DOM.render(React.createElement(Comments, null), document.body);`
We can see that `<Comments />` is transpiled directly into `React.createElement(Comments, null)`. This is where we can see what a Virtual DOM object actually is: a plain JavaScript Object that represents the tag to be rendered onto the screen.

Let’s inspect the output of `React.createElement()`:

```js
console.log(<div/>)
// or
console.log(React.createElement('div', null))
```
This gives us:

`{"type":"div","key":null,"ref":null,"props":{},"\_owner":null,"\_store":{}}`
See how the type is a string? `DOM.render({...})` gets this object above and looks at the type, and decides whether or not to reuse an existing `<div>` element on the DOM or create a new `<div>` and append it.

The Virtual DOM is not a simple Object – it is a recursive structure. For example, if we add two elements beneath the `<div/>`:

```js
console.log(<div><span/><button/></div>)
// or
console.log(React.createElement(
    'div',
    null,
    React.createElement('span', null),
    React.createElement('button', null)
))
```
What we get is a nested Object-tree:
```js
{
    "type":"div",
    "key":null,
    "ref":null,
    "props":{
    "children": [
            {"type":"span","key":null,"ref":null,"props":{}},
            {"type":"button","key":null,"ref":null,"props":{}}
        ]
    }
}
```
This is why, in a React Component’s code, we can access the child and ancestor elements via `this.props.children`. What React will do is walk down a very deep tree of nested Objects (depending on your UI complexity), each sitting in their parent element’s children.

One thing to note is that the type so far has just been a string. When a React Element is made from a custom Component (like Comments above), the type is a function:
```js
console.log(<Comments />)
// or
console.log(React.createElement(Comments, null))
```
gives us:
```js
{
    "key":null,
    "ref":null,
    "props":{},
    “type”: function Component() { ... }
}
```
