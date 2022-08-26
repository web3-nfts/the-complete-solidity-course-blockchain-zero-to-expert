# 54. Testing Message Updates

## **Testing Message Updates** 
-   `inbox.test.js`
    ```
    const assert = require("assert");
    const ganache = require("ganache-cli");
    const Web3 = require("web3");
    const web3 = new Web3(ganache.provider());
    const { interface, bytecode } = require("../compile");

    let accounts;
    let inbox;

    beforeEach(async () => {
        // Get a list of all accounts
        accounts = await web3.eth.getAccounts();
        inbox = await new web3.eth.Contract(JSON.parse(interface))
            .deploy({
            data: bytecode,
            arguments: ["Hi there!"],
            })
            .send({ from: accounts[0], gas: "1000000" });
    });

    describe("Inbox", () => {
        it("deploys a contract", () => {
            assert.ok(inbox.options.address);
        });
        it("has a default message", async () => {
            const message = await inbox.methods.message().call();
            assert.equal(message, "Hi there!");
        });
        it("can change the message", async () => {
            await inbox.methods.setMessage("bye").send({ from: accounts[0] });
            const message = await inbox.methods.message().call();
            assert.equal(message, "bye");
        });
    });

    ```
##  Testing with Mocha 

-   run Mocha test 
    ```
    npm run test
    ```

<details>
  <summary>run Mocha test - result</summary>

![54. Testing Message Updates](../imgs/54.1-testing-message.png)
---
</details>  

##  Ref 

27. [More on Running Functions Than You Want to Know](./27_More-on-Running-Functions-Than-You-Want-to-Know.md)
    ![](../imgs/27.3_More-on-Running-Functions-Than-You-Want-to-Know.png)

---

-   [54-testing-message.zip](https://github.com/web3-nfts/bt-web3/raw/main/Curricula/Ethereum-and-Solidity_The_Complete_Developers_Guide/resources/54-testing-message.zip)