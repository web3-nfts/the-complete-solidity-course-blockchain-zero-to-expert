#   43. Installing Modules

##  install mocha ganache-cli web3 (We no longer need a specific version of web3!)

```
npm install --save mocha ganache-cli web3
```

## **create inbox.test.js**
-   `inbox.test.js`
    ```
    const assert = require("assert");
    const ganache = require("ganache-cli");
    const Web3 = require("web3");
    const web3 = new Web3(ganache.provider());
    ```
