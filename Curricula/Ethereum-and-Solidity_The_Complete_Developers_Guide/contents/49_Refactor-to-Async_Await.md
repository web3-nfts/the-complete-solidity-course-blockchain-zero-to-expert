#   49. Refactor to Async/Await

## **Create inbox.test.js** - Refactor to Async/Await
-   `inbox.test.js`
    ```
    const assert = require("assert");
    const assert = require("assert");
    const ganache = require("ganache-cli");
    const Web3 = require("web3");
    const web3 = new Web3(ganache.provider());

    let accounts;

    beforeEach(async () => {
        // Get a list of all accounts
        accounts = await web3.eth.getAccounts();

        //  Use one of those accounts to deploy
        //  the contract
    });

    describe("Inbox", () => {
        it("deploys a contract", () => {
            console.log(accounts);
        });
    });
    ```
##  Testing with Mocha 

-   run Mocha test 
    ```
    npm run test
    ```

---

-   [49-refactor.zip](https://github.com/web3-nfts/bt-web3/raw/main/Curricula/Ethereum-and-Solidity_The_Complete_Developers_Guide/resources/49-refactor.zip)