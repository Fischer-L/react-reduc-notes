## React Updating Lifecycle

1. Parent `render()` called for updating

   1-1. Parent passes new props to Child

2. Child `componentWillReceiveProps(nextProps)`

   2-1. Child `setState(newState)` according to `nextProps` to update child's state. This won't trigger another `shouldComponentUpdate` but keep proceeding to (3) with the updated state.

3. Child `shouldComponentUpdate(nextProps, nextState)`
   
   3-1. Compare the new/old props and state and return `true` if should proceed to (4), otherwise `false`

4. Child `componentWillUpdate(nextProps, nextState)`

5. Child `render()` to render UI for user

6. Child `setState(newState)` based on some user actions. Go to (3) .


## React Component v.s. React Element v.s HTML Element

* React Component is `React.Component` class, which has `render` method to return one React element.

* React Element is a plain object carrying element type and data for rendering use. 

  It is created from React Component with `props`.

* The React built-in `div` element is the object for the HTML `div`.
  
  Calling `ReactDOM.render` againt it so will produce an instance of React `div` component.
  
  Calling the `render` method on the instance of React `div` component will return a HTML `<div>`.
  

```js
class Hello extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

// JSX style to create a React `Hello` element
let HelloElem1 = <Hello name="World"/>;
// Non-JSX style to create a React `Hello` element
let HelloElem2 = React.createElement(Hello, {name: 'World'}, null);

// `helloComponent1` is the instance of the `Hello` component
let helloComponent1 = ReactDOM.render(
  HelloElem1, // Passing HelloElem2 works the same 
  document.getElementById('root')
);

// `HelloElem1.type` is the `Hello` component so 
// below make a instance of the `Hello` component with the same `props` too.
let helloComponent2 = new HelloElem1.type();
helloComponent2.props = HelloElem1.props;
```
