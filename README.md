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


## How `create-react-app` magically builds with the webpack and start the webpack dev server

* In the package.json from running `npx create-react-app my-app`, we can know it go to `react-scripts` for helps.
  ```js
  {
     "scripts": {
       "start": "react-scripts start",
       "build": "react-scripts build",
       "test": "react-scripts test --env=jsdom",
       "eject": "react-scripts eject"
     }
   }
  ```
  So when we run `yarn start`, nodejs would invoke the react-scripts.js with the arg of `start`
  
* In the react-scripts.js, it runs the "../scripts/start.js" sync.
  ```js
  // ... ...
  const script = scriptIndex === -1 ? args[0] : args[scriptIndex];
  // ... ...
  switch (script) {
  case 'build':
  case 'eject':
  case 'start':
  case 'test': {
    const result = spawn.sync(
      'node',
      nodeArgs
        .concat(require.resolve('../scripts/' + script))
        .concat(args.slice(scriptIndex + 1)),
      { stdio: 'inherit' }
    );
  ```
  [1] https://github.com/facebook/create-react-app/blob/d9fbe448d729be7a96ffeedc77a9f4cd1b80213b/packages/react-scripts/bin/react-scripts.js#L35
  
* In the react-scripts's start.js, it creates a webpack compiler with the config path and the url params.
  ```js
    const webpack = require('webpack');
    const protocol = process.env.HTTPS === 'true' ? 'https' : 'http';
    const appName = require(paths.appPackageJson).name;
    const urls = prepareUrls(protocol, HOST, port);
    // Create a webpack compiler that is configured with custom messages.
    const compiler = createCompiler(
      webpack,
      config, // This the react-scripts's webpack.config.dev.js
      appName,
      urls,
      paths.useYarn
    );
  ```
  [1] https://github.com/facebook/create-react-app/blob/d9fbe448d729be7a96ffeedc77a9f4cd1b80213b/packages/react-scripts/scripts/start.js#L94
  
  Then, creates the webpack dev server and the dev server will listens to incoming requests.
  ```js
     const serverConfig = createDevServerConfig(
      proxyConfig,
      urls.lanUrlForConfig
    );
    const devServer = new WebpackDevServer(compiler, serverConfig);
    // Launch WebpackDevServer.
    devServer.listen(port, HOST, err => {
      if (err) {
        return console.log(err);
      }
      if (isInteractive) {
        clearConsole();
      }
      console.log(chalk.cyan('Starting the development server...\n'));
      openBrowser(urls.localUrlForBrowser);
    });
  ```
  [2] https://github.com/facebook/create-react-app/blob/d9fbe448d729be7a96ffeedc77a9f4cd1b80213b/packages/react-scripts/scripts/start.js#L109

* In the react-dev-utils's WebpackDevServerUtils.js, it does the `createCompiler` job to create a webpack compiler.
  ```js
  function createCompiler(webpack, config, appName, urls, useYarn) {
     // "Compiler" is a low-level interface to Webpack.
     // It lets us listen to some events and provide our own custom messages.
     let compiler;
     try {
       compiler = webpack(config, handleCompile);
     } catch (err) {
       // ... ...
     }
     
     // ... ...
  }
  ```
  [1] https://github.com/facebook/create-react-app/blob/e9abde739240b3124ab7237cbf32a370c209511e/packages/react-dev-utils/WebpackDevServerUtils.js#L116
