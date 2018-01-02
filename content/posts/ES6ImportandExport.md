---
title: "ES6ImportandExport"
date: 2018-01-01T20:27:19-05:00
draft: true
---

ES6 import and export statement allow to break up an application from one big file that contains everything into multiple files that communicate with each other. This makes the web apps scalable.

---

#### Import

1. Setup a super simple `webpack.config.js` for bundling.

   ```javascript
   const path = require('path');

   module.exports = {
       entry: './src/app.js',
       output: {
           path: path.join(__dirname, 'public'),
           filename: 'bundle.js'
       }
   };
   ```

2. Simple `index.html` for app entry.

   ```html
   <!DOCTYPE html>
   <html>
       <head>
           <meta charset="UTF-8">
           <title>Indecision App</title>
       </head>
       <body>
           <div id="app"></div>
           <script src="/bundle.js"></script>
       </body>
   </html>
   ```

   Nothing special at all.

3. Create `app.js`, which only have a simple `console.log` statement.

   ```javascript
   console.log('app.js is running');
   ```

   Now if we created `util.js` with

   ```javascript
   console.log('util.js is running');
   ```

   and we want to `import` it into `app.js`

4. Import `util.js` to `app.js`

   ```javascript
   import '/.util.js'
   console.log('app.js is running');
   ```

   Then refresh the browser, check the console, there should be 

   `util.js is running` at the first, then followed `app.js is running`.

   The Webpack console log should have the information of loading two separated files:

   ```
   [0] ./src/app.js 53 bytes {0} [built]
   [1] ./src/util.js 34 bytes {0} [built]
   ```









---

#### Export

There are two types of exports:

1. default export (only one)
2. named export (can be many)



**Named export**:

- Defined functions and export functions you want in `util.js`:

- ```javascript
  const square = (x) => x * x;
  const add = (x, y) => x + y;
  const canDrink = (age) => age >= 21;
  export { square, add, canDrink };
  ```

  or 

  ```javascript
  export const square = (x) => x * x;
  export const add = (x, y) => x + y;
  export const canDrink = (age) => age >= 21;
  ```

  ​	**They are same**.

  ​

- To use them outside of `util.js`, for example, use it in `app.js`

- ```javascript
  import { square, add, canDrink } from './util.js';
  console.log(square(6));
  console.log(add(200, 33));
  console.log(canDrink(22));
  ```

  Note: the import is a little bit different than before.

  The name inside { }, `square`, `add`, and `canDrink` **must match the name of the original function name.**



**Default export**:

- Define the function which will be the default to export:

- ```javascript
  const subtract = (x, y) => x - y;
  export {subtract as default};
  ```

  or 

  ```javascript
  const subtract = (x, y) => x - y;
  export default subtract;
  ```

  or could be as simple as

  ```javascript
  export default (x, y) => x - y;
  ```

  They are **all same**.



- To use it outside of `util.app`, for example, use it in `app.js`:

- ```javascript
  import subtract from './util.js';
  console.log(subtract(9, 2));
  ```

  Note: no {} around `subtract`.

  The name of the default export function **can be different** from the original:

  ```javascript
  import couldBeDifferent from './util.js';
  console.log(couldBeDifferent(9, 2));
  ```

  is same as the `substract`.