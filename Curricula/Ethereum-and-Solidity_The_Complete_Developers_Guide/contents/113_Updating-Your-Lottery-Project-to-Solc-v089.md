# 113. Updating Your Lottery Project to Solc v0.8.9

This lecture will walk you through all of the changes needed to bring your project up to date with the latest Solc v0.8.9.

**Environment Setup**

Due to expected dependency conflicts with old installed versions, it would be best to create a brand new project.

In your terminal of choice, run the following:

1.  mkdir lottery-updated
    ```
    mkdir lottery-updated
    ```

1.  cd lottery-updated
    ```
    cd lottery-updated
    ```

1.  npm init -y
    ```
    npm init -y
    ```

1.  install solc@0.8.9 web3 mocha ganache-cli @truffle/hdwallet-provider
    ```
    npm install solc@0.8.9 web3 mocha ganache-cli @truffle/hdwallet-provider
    ```

1.  Update your test script in the package.json file to be `"test": "mocha"`

1.  Copy your contracts directory (containing Lottery.sol), test directory (containing Lottery.test.js), compile.js, and deploy.js into the new lottery-updated project directory.

Here is the package.json file for reference (your versions should not be lower than those specified below):

```
{
    "name": "lottery-updated",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "test": "mocha"
    },
    "author": "",
    "license": "ISC",
    "dependencies": {
        "@truffle/hdwallet-provider": "2.0.1",
        "ganache-cli": "^6.12.2",
        "mocha": "^9.1.2",
        "solc": "^0.8.9",
        "web3": "^1.6.0"
    }
}
```

**Lottery.sol**
```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.9;

contract Lottery {
    address public manager;
    address payable[] public players;
    
    constructor() {
        manager = msg.sender;
    }
    
    function enter() public payable {
        require(msg.value > .01 ether);
        players.push(payable(msg.sender));
    }
    
    function random() private view returns (uint) {
        return uint(keccak256(abi.encodePacked(block.difficulty, block.timestamp, players)));
    }
    
    function pickWinner() public restricted {
        uint index = random() % players.length;
        players[index].transfer(address(this).balance);
        players = new address payable[](0);
    }
    
    modifier restricted() {
        require(msg.sender == manager);
        _;
    }
    
    function getPlayers() public view returns (address payable[] memory) {
        return players;
    }
}   
```
Outline of changes:

1.  Update the pragma version at the top of the contract file to **^0.8.9**
1.  Add an SPDX identifier to the top of the contract (will address compilation warnings) - [Source](https://docs.soliditylang.org/en/v0.8.9/layout-of-source-files.html).
1.  Refactor the Lottery constructor to use the new **constructor** keyword - [Source](https://docs.soliditylang.org/en/v0.8.9/050-breaking-changes.html).
1.  Remove the **public** keyword from the constructor - [Source](https://docs.soliditylang.org/en/v0.8.9/070-breaking-changes.html?highlight=constructor).
1.  Update all instances of **address[]** to the new **address payable[]** type - [Source](https://docs.soliditylang.org/en/latest/050-breaking-changes.html#explicitness-requirements).
1.  Specify the data location of address payable[] in the getPlayers function to be **memory** - [Source](https://docs.soliditylang.org/en/v0.8.9/050-breaking-changes.html#explicitness-requirements).
1.  Update the keccak256 hash function to use **abi.encodePacked** - [Source](https://docs.soliditylang.org/en/v0.8.11/050-breaking-changes.html#semantic-and-syntactic-changes).
1.  In the pickWinner function, explicitly convert contract type to address: **address(this).balance** - [Source](https://docs.soliditylang.org/en/v0.8.11/050-breaking-changes.html).
1.  In the enter function, explicitly convert msg.sender to type address payable: **payable(msg.sender)** - [Source](https://docs.soliditylang.org/en/v0.8.11/080-breaking-changes.html#new-restrictions).

**compile.js**
```
const path = require('path');
const fs = require('fs');
const solc = require('solc');
 
const lotteryPath = path.resolve(__dirname, 'contracts', 'Lottery.sol');
const source = fs.readFileSync(lotteryPath, 'utf8');
 
const input = {
  language: 'Solidity',
  sources: {
    'Lottery.sol': {
      content: source,
    },
  },
  settings: {
    outputSelection: {
      '*': {
        '*': ['*'],
      },
    },
  },
};
 
module.exports = JSON.parse(solc.compile(JSON.stringify(input))).contracts['Lottery.sol'].Lottery;
```
Outline of changes:
1.  Add the expected JSON formatted input, specifying the language, sources, and outputSelection - [Source](https://docs.soliditylang.org/en/v0.8.7/using-the-compiler.html#compiler-input-and-output-json-description).
1.  Update the export to provide the expected JSON formatted output - [Source](https://github.com/ethereum/solc-js#example-usage-without-the-import-callback)
1.  Note - the output is structured differently so the accessors have changed slightly. If you have a doubt you can add a console.log to view this structure:

a.  1
```
console.log(JSON.parse(solc.compile(JSON.stringify(input))).contracts);
```
**Lottery.test.js**
```
const assert = require('assert');
const ganache = require('ganache-cli');
const Web3 = require('web3');
const web3 = new Web3(ganache.provider());
 
const { abi, evm } = require('../compile');
 
let lottery;
let accounts;
 
beforeEach(async () => {
  accounts = await web3.eth.getAccounts();
 
  lottery = await new web3.eth.Contract(abi)
    .deploy({ data: evm.bytecode.object })
    .send({ from: accounts[0], gas: '1000000' });
});
describe('Lottery Contract', () => {
  it('deploys a contract', () => {
    assert.ok(lottery.options.address);
  });
 
  it('allows one account to enter', async () => {
    await lottery.methods.enter().send({
      from: accounts[0],
      value: web3.utils.toWei('0.02', 'ether'),
    });
 
    const players = await lottery.methods.getPlayers().call({
      from: accounts[0],
    });
 
    assert.equal(accounts[0], players[0]);
    assert.equal(1, players.length);
  });
 
  it('allows multiple accounts to enter', async () => {
    await lottery.methods.enter().send({
      from: accounts[0],
      value: web3.utils.toWei('0.02', 'ether'),
    });
    await lottery.methods.enter().send({
      from: accounts[1],
      value: web3.utils.toWei('0.02', 'ether'),
    });
    await lottery.methods.enter().send({
      from: accounts[2],
      value: web3.utils.toWei('0.02', 'ether'),
    });
 
    const players = await lottery.methods.getPlayers().call({
      from: accounts[0],
    });
 
    assert.equal(accounts[0], players[0]);
    assert.equal(accounts[1], players[1]);
    assert.equal(accounts[2], players[2]);
    assert.equal(3, players.length);
  });
 
  it('requires a minimum amount of ether to enter', async () => {
    try {
      await lottery.methods.enter().send({
        from: accounts[0],
        value: 0,
      });
      assert(false);
    } catch (err) {
      assert(err);
    }
  });
 
  it('only manager can call pickWinner', async () => {
    try {
      await lottery.methods.pickWinner().send({
        from: accounts[1],
      });
      assert(false);
    } catch (err) {
      assert(err);
    }
  });
 
  it('sends money to the winner and resets the players array', async () => {
    await lottery.methods.enter().send({
      from: accounts[0],
      value: web3.utils.toWei('2', 'ether'),
    });
 
    const initialBalance = await web3.eth.getBalance(accounts[0]);
    await lottery.methods.pickWinner().send({ from: accounts[0] });
    const finalBalance = await web3.eth.getBalance(accounts[0]);
    const difference = finalBalance - initialBalance;
 
    assert(difference > web3.utils.toWei('1.8', 'ether'));
  });
});
```
Outline of changes:

1.  Update the import to destructure the **abi** (formerly the interface) and the **evm** (bytecode)
```
const { abi, evm } = require('../compile');
```

1.  Pass the **abi** to the contract object
```
lottery = await new web3.eth.Contract(abi)
```

1.  Assign the bytecode to the data property of the deploy method:
```
.deploy({
    data: evm.bytecode.object, 
```

**deploy.js**
```
const HDWalletProvider = require('@truffle/hdwallet-provider');
const Web3 = require('web3');
const { abi, evm } = require('./compile');
 
const provider = new HDWalletProvider(
  'YOUR_MNEMONIC',
  'YOUR_INFURA_URL'
);
 
const web3 = new Web3(provider);
 
const deploy = async () => {
  const accounts = await web3.eth.getAccounts();
 
  console.log('Attempting to deploy from account', accounts[0]);
 
  const result = await new web3.eth.Contract(abi)
    .deploy({ data: evm.bytecode.object })
    .send({ gas: '1000000', from: accounts[0] });
 
  console.log(JSON.stringify(abi));
  console.log('Contract deployed to', result.options.address);
  provider.engine.stop();
};
deploy();
```
Outline of changes:

1.  Update the import to use the newer **@truffle/hdwallet-provider** module.
1.  Update the import to destructure the **abi** (formerly the interface) and the **evm** (bytecode)
    ```
    const { abi, evm } = require('./compile');
    ```
1.  Pass the **abi** to the contract object:
    ```
    const result = await new web3.eth.Contract(abi)
    ```
1.  Assign the bytecode to the data property of the deploy method:
    ```
    .deploy({
      data: evm.bytecode.object, 
    ```  
1.  Stringify the abi so that we can console.log it properly: **console.log(JSON.stringify(abi));**
1.  Call **provider.engine.stop()** to prevent deployment from hanging in the terminal - [Source](https://github.com/trufflesuite/truffle/tree/master/packages/hdwallet-provider#general-usage)
1.  Run **node deploy.js** to deploy a new contract and print the new contract address and abi to the terminal.
1.  Update **lottery.js** in the lottery-react frontend with the new contract address and abi.

**Completed Code**

Completed project with all changes described above can be found attached to this lecture note as a zip file.


##  Resources for this lecture

---

-   [lottery-updated.zip](https://github.com/web3-nfts/bt-web3/raw/main/Curricula/Ethereum-and-Solidity_The_Complete_Developers_Guide/resources/lottery-updated.zip)

---

-   [lottery-react.zip](https://github.com/web3-nfts/bt-web3/raw/main/Curricula/Ethereum-and-Solidity_The_Complete_Developers_Guide/resources/lottery-react.zip)