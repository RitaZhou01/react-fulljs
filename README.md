# react-fulljs
A simple project about creating a local development environment for Node and React 

# Initializing


```
$ npm init -y
```

You can use the bellow code to generate your package.json file with default values.

# Installing Main Dependencies


```
$ npm i express
```

This command will download the express npm packge, place it under the node_modules folder and save this dependency to your package.json file. 


```
$ npm i react react-dom
```

The frontend dependencies you need are React and ReactDOM.


```
$ npm i webpack webpack-cli
```

Since you’ll be writing your code in multiple modules (files) and it will depend on other modules (like React), you need a module bundler to translate all these modules into something that can work in all browsers today. You can use Webpack for that job.

```
$ npm i babel-loader @babel/core @babel/node @babel/preset-env @babel/preset-react
```

Webpack is just a generic module bundler. You need to configure it with loaders to transform code from one state into the other. For example, you need to transform React’s JSX code into React’s API calls. The tool for that job is Babel. Besides JSX, Babel can also transform modern JavaScript features into code that can be understood in any execution environment. 

# Installing Development Dependencies


```
$ npm i -D nodemon
```

-D means you can save the dependencies under a devDependencies section in package.json.

When you run a Node server and then change the code of that server, you need to restart Node. This will be a frustrating thing in development. Luckily, there are some workarounds. The most popular one is Nodemon.


```
$ npm i -D eslint babel-eslint eslint-plugin-react
```

ESLint is a code quality tool and if you don’t use it, your code will not be as good as it could be.

To configure ESLint, you need to add a .eslintrc.js file in the root of the project.


```
module.exports = {
  parser: 'babel-eslint',
  env: {
    browser: true,
    commonjs: true,
    es6: true,
    node: true,
    jest: true,
  },
  extends: ['eslint:recommended', 'plugin:react/recommended'],
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    sourceType: 'module',
  },
  plugins: ['react'],
  rules: {
    // You can do your customizations here...
    // For example, if you don't want to use the prop-types package,
    // you can turn off that recommended rule with: 'react/prop-types': ['off']
  },
};
```



```
$ npm i -D jest babel-jest react-test-renderer
```
The most popular testing library that’s usually used with React is Jest. 

# Creating an Initial Directory Structure

This really depends on your style and preferences

```
fulljs/
  dist/
    main.js
  src/
    index.js
    components/
      App.js
    server/
      server.js
```

# Configuring Webpack and Babel

### Create file fulljs/babel.config.js with the following code

```
module.exports = {
  presets: ['@babel/preset-env', '@babel/preset-react'],
};
```
### Create file fulljs/webpack.config.js with the following code

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
        },
      },
    ],
  },
};
```
# Creating npm Scripts for Development

You need 2 commands to run this environment. You need to run your web server and you need to run Webpack to bundle the frontend application for browsers. You can use npm scripts to manage these.


```
// In package.json
  scripts: {
    "test": "jest",
    "dev-server": "nodemon --exec babel-node src/server/server.js --ignore dist/",  // run the server file with Nodemon and make it work with the same Babel configuration.
    "dev-bundle": "webpack -wd" // a simple runner for webpack
  }
```

The -w flag in the command above is to run Webpack in watch mode as well and the -d flag is a set of built-in configurations to make Webpack generate a development-friendly bundle.

#  Testing Everything with a Sample React Application

Add following files.

### src/components/App.js

```
import React, {useState} from 'react';

export default function App () {
  const [count, setCount] = useState(0);
  return (
    <div>
      This is a sample stateful and
      server-side rendered React application.
      <br /><br />
      Here is a button that will track how many times you click it:
      <br /><br />
      <button onClick={() => setCount(count+1)}>
        {count}
      </button>
    </div>
  );
}
```

### src/index.js


```
import React from 'react';
import ReactDOM from 'react-dom';

import App from './components/App';

ReactDOM.hydrate(
  <App />,
  document.getElementById('mountNode'),
);
```

### src/server/server.js

```
import express from 'express';
import React from 'react';
import ReactDOMServer from 'react-dom/server';
import App from '../components/App';

const server = express();
server.use(express.static('dist'));

server.get('/', (req, res) => {
  const initialMarkup = ReactDOMServer.renderToString(<App />);

  res.send(`
    <html>
      <head>
        <title>Sample React App</title>
      </head>
      <body>
        <div id="mountNode">${initialMarkup}</div>
        <script src="/main.js"></script>
      </body>
    </html>
  `)
});

server.listen(4242, () => console.log('Server is running...'));
```

That’s it. If you run both npm dev-server and dev-bundle scripts (in 2 separate terminals):


```
$ npm run dev-server
$ npm run dev-bundle
```

Then open up your browser on http://localhost:4242/, you should see the React application rendered.




