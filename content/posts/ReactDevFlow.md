---
title: "ReactDevFlow"
date: 2017-11-17T19:44:59-05:00
draft: true
---

The modern front end development has specific steps to do:

- Environment setup
- Development
- Module bundler
- Configuration for deploy



This is note will write some details for React development workflow.

---

Tools:

- visual studio code
- Node.js
- NPM (or `yarn` depends on favor)
  - to install yarn, simply run `npm install -g yarn`
- Webpack

---

Super simple development environment setup:

- Local Web Server `live-server` (will be replaced with `parcel`)
  - `npm install -g live-server` or `yarn global add live-server` to install it
  - `live-server public` to run the local server with folder name `public`

---

#### First step of React using __*CDN*__:

- `npm init` to initialize the project


- Create two files `index.html` and `app.js` inside folder `public`


- Integrate react into `index.html`:

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <meta http-equiv="X-UA-Compatible" content="ie=edge">
      <title>React</title>
  </head>
  <body>
      <!-- React library through CDN -->
      <script src="https://unpkg.com/react@16/umd/react.development.js"></script>
      <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
      <!-- the acutal app is here -->
      <script src="/scripts/app.js"></script>
  </body>
  </html>
  ```

- Inside `app.js`, simple add some code for testing:

  ```javascript
  console.log("App is running!");
  const template = <p>JSX from app.js!</p>;
  let appRoot = document.getElementById('app');
  ReactDOM.render(template, appRoot);
  ```
  ​

- Run `live-server public` in terminal to check the result.


---

A **compiler** is needed with above setup:

There will be a `Uncaught SyntaxError` in the browser

​