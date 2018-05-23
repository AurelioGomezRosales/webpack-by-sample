# 00 TypeScript

In this demo we add support for TypeScript. We do the transpilation process in two steps:
  - From Typescript to ES6 using Typescript plumbing.
  - From ES6 to ES5 using babel.

¿Why this approach? We want our project to be as standard as possible. If you transpile from ES6 to ES5 using babel you use the same approach as many existing projects and libraries.

We start from sample _01 Styles/03 SASS_, install TypeScript locally, configure a tsconfig file, add some ts files, install awesome-typescript-loader and apply it to webpackconfig.

Summary steps: 
 - Install TypeScript as a local dependency.
 - Configure TypeScript for our project (tsconfig).
 - Port our project to TypeScript and add to our code some TypeScript features.
 - Install [awesome-typescript-loader](https://github.com/s-panferov/awesome-typescript-loader).
 - Add the proper configuration in `webpack.config.js`.

## Prerequisites

You need to have nodejs installed in your computer (at least v. 8.9.2). If you want to follow this guide you need to take as starting point sample _03 SASS_.

## steps

- _npm install_ to install the previous sample packages:

```bash
npm install
```

- TypeScript is a nice superset of JavaScript that allows static typings amongst other interesting features. All the code we type in TypeScript does not run on the browser, so we need to transpile it. Let's start by installing TypeScript locally(\*).

```
npm install typescript --save-dev
```

_(*) Why installing TypeScript locally and not globally? By installing it locally the project does not depend on global dependencies and is easier to e.g make a build and pass unit tests on a clean CI (Continuous Integration) machine like [Travis](https://travis-ci.org/), [Docker](https://www.docker.com/), [Jenkins](https://jenkins.io/), etc.

Another benefit of installing locally is that we can set up a specific version of TypeScript with no dependency on the version that is globally installed on the machine where the code is getting transpiled._

- The next step is to add a TypeScript configuration file, *`tsconfig.json`*.
In this file we define the settings that we want, e.g to transpile to ES5 amongst others.

_./tsconfig.json_

```json
{
  "compilerOptions": {
    "target": "es6",
    "module": "es6",
    "moduleResolution": "node",
    "declaration": false,
    "noImplicitAny": false,
    "jsx": "react",
    "sourceMap": true,
    "noLib": false,
    "suppressImplicitAnyIndexErrors": true
  },
  "compileOnSave": false,
  "exclude": [
    "node_modules"
  ]
}
```

- In order to get JQuery intellisense, we can install JQuery typings:

```
npm install @types/jquery -d
```

- Let's port our code to TypeScript. We are going to rename the files *`students.js`* and *`averageService.js`* to _students.**ts**_ and _averageService.**ts**_.


- Let's introduce some TypeScript. In *`students.ts`* we type the variables we are using:

_./src/students.ts_
```diff
import {getAvg} from "./averageService";

$('body').css('background-color', 'lightSkyBlue');

- const scores = [90, 75, 60, 99, 94, 30];
+ const scores: number[] = [90, 75, 60, 99, 94, 30];
- const averageScore = getAvg(scores);
+ const averageScore: number = getAvg(scores);

- const messageToDisplay = `average score ${averageScore}`;
+ const messageToDisplay: string = `average score ${averageScore}`;

document.write(messageToDisplay);
```

- Next we type our function in *`averageService.ts`*:

```diff
- export function getAvg(scores) {
+ export function getAvg(scores: number[]): number {
   return getTotalScore(scores) / scores.length;
  }

- function getTotalScore(scores) {
+ function getTotalScore(scores: number[]): number {
    return scores.reduce((score, count) => {
      return score + count;
    });
  }
```

- Now it's time to configure *wepback*. Let's install a loader that to manage TypeScript: [awesome-typescript-loader](https://github.com/s-panferov/awesome-typescript-loader).

```
npm install awesome-typescript-loader --save-dev
```

- Let's update *`webpack.config.js`*. In order to use this loader on **ts** extension files, and to avoid having to add the extensions to the ts imports, we can just add it to the array of extensions to be resolved:

_./webpack.config.js_
```diff
module.exports = {
  context: path.join(basePath, 'src'),
+  resolve: {
+    extensions: ['.js', '.ts']
+  },  
  entry: {
-    app: './students.js',
+    app: './students.ts'
    appStyles: [
      './mystyles.scss',
    ],
    vendor: [
      'jquery',
    ],
    vendorStyles: [
      '../node_modules/bootstrap/dist/css/bootstrap.css',
    ],
  },
```

- And let's configure the proper loader for the _.ts_ extension

_./webpack.config.js_
```diff
  module: {
    rules: [
+      {
+        test: /\.(ts|tsx)$/,
+        exclude: /node_modules/,
+        loader: 'awesome-typescript-loader',
+        options: {
+          useBabel: true,
+        },
+      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
      },
```
- If we run the app (`npm start`) we can check that everything works as expected.

- If we open a browser console we can access the `.ts` files and add breakpoints thanks to the previous `sourceMap` configuration:

### ./tsconfig.json
```javascript
{
  "compilerOptions": {
    ...
    "sourceMap": true,
    ...
  },
  ...
}

```

### ./webpack.config.js
```javascript
...
  // For development https://webpack.js.org/configuration/devtool/#for-development
  devtool: 'inline-source-map',
  ...

```
