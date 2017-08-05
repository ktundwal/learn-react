# React Pre-requisites

## Node and NPM

```
node --version
*v8.1.3
npm --version
*v5.0.4
```

## React

Install react. Dont forget to save it to `package.json`
```
npm install --save react
```
Not every project folder comes with a package.json file though. Create one.
```
npm init -y
```

> Above adds React to an application. You would have to deal with Babel to make your application aware of JSX - the React syntax - and JavaScript ES6. Babel transpiles your code that browsers can interpret ES6 and JSX. Not all browsers are capable of interpreting the syntax. The setup includes a lot of configuration and tooling

## Zero config setup

```
npm install -g create-react-app
create-react-app --version
create-react-app helloworld
cd helloworld
```

This creates a folder structure.

* All source is in `/src/App.js`
* `src/App.test.js` file for tests
* and a `src/index.js` as entry point to the React world
* find the `package.json` file and `node_modules/` folder to manage your node packages.
* The `create-react-app` application is a npm project

## Run the app

```
// Runs the application in http://localhost:3000
npm start

// Runs the tests
npm test

// Builds the application for production
npm run build
```