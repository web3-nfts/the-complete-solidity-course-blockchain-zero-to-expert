# 96. Boilerplate and React App Updates - Do Not Skip

Over the next few lectures, we will be generating a React app and installing some dependencies.

**We have provided a boilerplate that you can use to avoid version issues or conflicts**. Download the boilerplate attached to this lecture to where you typically develop and run `npm install`.

*Note - If you are using this boilerplate, you will not need to install any dependencies for this project going forward.*

##  **Required Updates for those not using Boilerplate.**

In the upcoming lecture, we will be generating our first React project by using Create React App. As of v3.3.0, the library has [dropped support for global installs](https://create-react-app.dev/docs/getting-started/#quick-start).

**The recommended method for generating a project is now:**

```
npx create-react-app lottery-react
```

If you get any errors about missing templates or how a global Create React App install is no longer supported even when using this command, you likely need to remove the global package from your system:

```
npm uninstall -g create-react-app
```

**Note** - some students have mentioned that an extra step was needed for Mac / Linux users to manually delete the folder:

```
rm -rf /usr/local/bin/create-react-app
```

**Refactor the functional component to be a class component**

Create React App will now automatically generate a functional App.js instead of a class component.

Your App.js will need to be refactored to look like this:

```
import logo from "./logo.svg";
import "./App.css";
import React from "react";
 
class App extends React.Component {
  render() {
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


##  Resources for this lecture

---

-   [lottery-react-boilerplate.zip](https://github.com/web3-nfts/bt-web3/raw/main/Curricula/Ethereum-and-Solidity_The_Complete_Developers_Guide/resources/lottery-react-boilerplate.zip)