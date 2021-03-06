# Set up Karma with Jasmine in [Create React App](https://github.com/facebookincubator/create-react-app)

**This guide will use specific versions NPM packages. Please double-check that you installed the same versions to avoid installation issues!**

1. Install [Create React App](https://github.com/facebookincubator/create-react-app)

  ```
  $ npm install -g create-react-app@1.3.1
  ```

2. Create a new React application and eject from it

  ```
  $ create-react-app my-app
  $ cd my-app
  $ npm run eject
  ```

  Press `y` to confirm that you want to eject.

3. Install Karma, Karma related libraries, and Babel Polyfill

  ```
  $ npm i -D karma@1.3.0 karma-sourcemap-loader@0.3.7 karma-webpack@2.0.1
  $ npm i -D karma-phantomjs-launcher@1.0.2 babel-polyfill@6.22.0
  ```

4. Install Jasmine and its Karma adapter

  ```
  $ npm i -D jasmine-core@2.5.2 karma-jasmine@1.1.0
  ```

5. Install `react-page-object`, `enzyme`, and `react-test-renderer`

  ```
  $ npm i -D react-page-object enzyme@2.8.2 react-test-renderer@15.5.4
  ```

6. Modify your `package.json` `scripts` to be

  ```json
  "scripts": {
    "start": "node scripts/start.js",
    "build": "node scripts/build.js",
    "test": "npm run karma -- --single-run",
    "karma": "BABEL_ENV=development NODE_ENV=test node_modules/karma/bin/karma start config/karma.conf.js"
  },
  ```

7. Create a Karma configuration file

  ```
  $ touch config/karma.conf.js
  ```

8. Add the following to your `config/karma.conf.js` file

  ```js
  var path = require('path');
  var testHelperPath = path.resolve('test/testHelper.js')

  module.exports = function(config) {
    config.set({
      // use the PhantomJS browser
      browsers: ['PhantomJS'],
      // use Jasmine
      frameworks: ['jasmine'],

      // files that Karma will server to the browser
      files: [
        // entry file for Webpack
        testHelperPath
      ],

      // before serving test/testHelper.js to the browser
      preprocessors: {
        [testHelperPath]: [
          // use karma-webpack to preprocess the file via webpack
          'webpack',
          // use karma-sourcemap-loader to utilize sourcemaps generated by webpack
          'sourcemap'
        ]
      },

      // webpack configuration used by karma-webpack
      webpack: {
        // generate sourcemaps
        devtool: 'inline-source-map',
        // enzyme-specific setup
        externals: {
          'cheerio': 'window',
          'react/addons': true,
          'react/lib/ExecutionEnvironment': true,
          'react/lib/ReactContext': true
        },
        module: {
          // lint JavaScript with Eslint
          preLoaders: [
            {
              test: /\.(js|jsx)$/,
              exclude: /node_modules/,
              loader: 'eslint'
            }
          ],
          // use same loaders as Create React App
          loaders: [
            {
              exclude: [
                /\.(js|jsx)$/,
                /\.css$/,
                /\.json$/,
              ],
              loader: 'file',
              query: {
                name: 'static/media/[name].[hash:8].[ext]'
              }
            },
            {
              test: /\.(js|jsx)$/,
              exclude: /node_modules/,
              loader: 'babel'
            },
            {
              test: /\.css$/,
              loader: 'style!css'
            },
            {
              test: /\.json$/,
              loader: 'json'
            }
          ]
        },
        // relative path starts out at the src folder when importing modules
        resolve: {
          root: path.resolve('./src')
        }
      },

      webpackMiddleware: {
        // only output webpack error messages
        stats: 'errors-only'
      },
    })
  }
  ```

9. In the application root folder, create a `test` folder and a `testHelper.js` file to it.

  ```
  $ mkdir test
  $ touch test/testHelper.js
  ```

10. Add the following to your `test/testHelper.js` file.

  ```js
  // polyfill PhantomJS environment
  import 'babel-polyfill'
  import 'whatwg-fetch'

  // require all the test files in the test folder that end with Spec.js or Spec.jsx
  const testsContext = require.context(".", true, /Spec.jsx?$/);
  testsContext.keys().forEach(testsContext);

  // output at when the test were run
  console.info(`TESTS RAN AT ${new Date().toLocaleTimeString()}`);
  ```

11. Create a passing test file

  ```
  $ touch test/AppSpec.js
  ```

12. Add the following content to `test/AppSpec.js`

  ```js
  import Page from 'react-page-object'
  import React from 'react'
  import App from 'App'

  describe('AppSpec', () => {
    let page

    beforeEach(() => {
      page = new Page(<App />)
    })

    afterEach(() => {
      page.destroy()
    })

    it('should pass', () => {
      expect(page.content()).toMatch(/Welcome to React/)
    })
  })
  ```

At this point, the set up is complete! You can run all your tests once with

```
$ npm test
```

Or continuously with

```
$ npm run karma
```
