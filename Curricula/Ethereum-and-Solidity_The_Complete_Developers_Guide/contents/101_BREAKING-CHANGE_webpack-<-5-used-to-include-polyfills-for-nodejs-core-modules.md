# 101. BREAKING CHANGE: webpack < 5 used to include polyfills for node.js core modules

In the upcoming lecture, after adding the web3 imports to your application, you may receive the following error:

*BREAKING CHANGE: webpack < 5 used to include polyfills for node.js core modules by default....*

**Reason:**

Create React App just [released v5](https://github.com/facebook/create-react-app/releases/tag/v5.0.0) which now implements Webpack v5. This is going to fully break many libraries including web3 as the required Node core library polyfills will be missing. Theoretically, we might be able to resolve this by [ejecting CRA](https://create-react-app.dev/docs/available-scripts/#npm-run-eject) and adding a Webpack [polyfill plugin](https://github.com/Richienb/node-polyfill-webpack-plugin), or, adding the fallbacks manually similar to what the [Angular community has had to do](https://github.com/ChainSafe/web3.js#web3-and-angular). However, this is more advanced and far out of scope for this course.

**Solution:**

**Until CRA either [provides a workaround](https://github.com/facebook/create-react-app/pull/11764) (there is a current pull request) to avoid ejecting, or, web3 somehow addresses this, I would recommend the following:**

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

You can also just use the completed code attached to the [Web3 Setup](https://www.udemy.com/course/ethereum-and-solidity-the-complete-developers-guide/learn/lecture/9020584#questions) lecture, which includes the currently supported version of Create React App.