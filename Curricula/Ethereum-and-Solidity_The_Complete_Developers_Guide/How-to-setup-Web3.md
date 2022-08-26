# How to setup Web3 - lottery-react

**Solution: BREAKING CHANGE: webpack < 5 used to include polyfills for node.js core modules by default....**

Open the lottery-react project's package.json file in a code editor and change this line:

    ```
    "react-scripts": "^5.0.0",
    ```

to this:

    ```
    "react-scripts": "4.0.3",
    ```

Then, using your terminal, change into the root of the lottery-react project directory and run the following:

    ```
    rm -r node_modules
    ```

    ```
    rm package-lock.json
    ```

    ```
    npm install
    ```


**Install web3**
```
npm install web3
```
**Create src/web3.js**
```
import Web3 from "web3";

window.ethereum.request({ method: "eth_requestAccounts" });

const web3 = new Web3(window.ethereum);

export default web3;

/*
import Web3 from 'web3';
const web3 = new Web3(window.web3.currentProvider);
export default web3;
*/
```

**app.js**
```
import logo from "./logo.svg";
import "./App.css";
import React from "react";
import web3 from './web3';
 
class App extends React.Component {
  render() {
    console.log(web3.version)
    web3.eth.getAccounts().then(console.log);
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <p>
            Edit <code>src/App.js</code> and save to reload.
          </p>
          <a
            className="App-link"
            href="https://reactjs.org"
            target="_blank"
            rel="noopener noreferrer"
          >
            Learn React
          </a>
        </header>
      </div>
    );
  }
}
export default App;
```

**npm run start**
```
npm run start
```