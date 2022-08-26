#   64. Updating Your Inbox Project to Solc v0.8.9

This lecture will walk you through all of the changes needed to bring your project up to date with the latest Solc v0.8.9.

##  Environment Setup

Due to expected dependency conflicts with old installed versions, it would be best to create a brand new project.

In your terminal of choice, run the following:

1.  ```mkdir inbox-updated```
1.  ```cd inbox-updated```
1.  ```npm init -y```
1.  ```npm install solc@0.8.9 web3 mocha ganache-cli @truffle/hdwallet-provider```
1.  Update your test script in the package.json file to be ```"test": "mocha"```
1.  Copy your contracts directory (containing Inbox.sol), test directory (containing Inbox.test.js), compile.js, and deploy.js into the new inbox-updated project directory.

Here is the package.json file for reference (your versions should not be lower than those specified below):

```
{
  "name": "inbox-updated",
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

`Inbox.sol`
```
// SPDX-License-Identifier: MIT
 
pragma solidity ^0.8.9;
 
contract Inbox {
    string public message;
    
    constructor(string memory initialMessage) {
        message = initialMessage;
    }
    
    function setMessage(string memory newMessage) public {
        message = newMessage;
    }
}
```

Outline of changes:

1.  Update the pragma version at the top of the contract file to **^0.8.9**
1.  Refactor the Inbox constructor to use the new **constructor** keyword - [Source](https://docs.soliditylang.org/en/v0.8.9/050-breaking-changes.html).
1.  Specify the data location of the variables to be **memory** - [Source](https://docs.soliditylang.org/en/v0.8.9/050-breaking-changes.html#explicitness-requirements).
1.  Remove the **public** keyword from the constructor - [Source](https://docs.soliditylang.org/en/v0.8.9/070-breaking-changes.html?highlight=constructor).
1.  Add an SPDX identifier to the top of the contract (will address compilation warnings) - [Source](https://docs.soliditylang.org/en/v0.8.9/layout-of-source-files.html).

**compile.js**
```
const path = require('path');
const fs = require('fs');
const solc = require('solc');
 
const inboxPath = path.resolve(__dirname, 'contracts', 'Inbox.sol');
const source = fs.readFileSync(inboxPath, 'utf8');
 
const input = {
  language: 'Solidity',
  sources: {
    'Inbox.sol': {
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
 
module.exports = JSON.parse(solc.compile(JSON.stringify(input))).contracts[
  'Inbox.sol'
].Inbox;
```
Outline of changes:

1.  Add the expected JSON formatted input, specifying the language, sources, and outputSelection - [Source](https://docs.soliditylang.org/en/v0.8.7/using-the-compiler.html#compiler-input-and-output-json-description).
1.  Update the export to provide the expected JSON formatted output - [Source](https://github.com/ethereum/solc-js#example-usage-without-the-import-callback)
1.  Note - the output is structured differently so the accessors have changed slightly. If you have a doubt you can add a console.log to view this structure:
a.  
    console.log(JSON.parse(solc.compile(JSON.stringify(input))).contracts);
This will return the following:
```
a.  {
b.    'Inbox.sol': {
c.      Inbox: {
d.        abi: [Array],
e.        devdoc: [Object],
f.        evm: [Object],
g.        ewasm: [Object],
h.        metadata: '{...}',
i.        storageLayout: [Object],
j.        userdoc: [Object]
k.      }
l.    }
m.  }
```

**Inbox.test.js**

```
const assert = require('assert');
const ganache = require('ganache-cli');
const Web3 = require('web3');
const web3 = new Web3(ganache.provider());
 
const { abi, evm } = require('../compile');
 
let accounts;
let inbox;
 
beforeEach(async () => {
  // Get a list of all accounts
  accounts = await web3.eth.getAccounts();
  inbox = await new web3.eth.Contract(abi)
    .deploy({
      data: evm.bytecode.object,
      arguments: ['Hi there!'],
    })
    .send({ from: accounts[0], gas: '1000000' });
});
 
describe('Inbox', () => {
  it('deploys a contract', () => {
    assert.ok(inbox.options.address);
  });
  it('has a default message', async () => {
    const message = await inbox.methods.message().call();
    assert.equal(message, 'Hi there!');
  });
  it('can change the message', async () => {
    await inbox.methods.setMessage('bye').send({ from: accounts[0] });
    const message = await inbox.methods.message().call();
    assert.equal(message, 'bye');
  });
});
```
Outline of changes:

1.  Update the import to destructure the **abi** (formerly the interface) and the **evm** (bytecode)
    ```const { abi, evm } = require('../compile');```

2.  Pass the **abi** to the contract object
  inbox = await new web3.eth.Contract(abi)

3.  Assign the bytecode to the data property of the deploy method:

a.    .deploy({
b.      data: evm.bytecode.object, 

**deploy.js**

```
const HDWalletProvider = require('@truffle/hdwallet-provider');
const Web3 = require('web3');
 
const { abi, evm } = require('./compile');
 
provider = new HDWalletProvider(
  'YOUR_MNEMONIC',
  'YOUR_INFURA_URL'
);
 
const web3 = new Web3(provider);
 
const deploy = async () => {
  const accounts = await web3.eth.getAccounts();
 
  console.log('Attempting to deploy from account', accounts[0]);
 
  const result = await new web3.eth.Contract(abi)
    .deploy({ data: evm.bytecode.object, arguments: ['Hi there!'] })
    .send({ gas: '1000000', from: accounts[0] });
 
  console.log('Contract deployed to', result.options.address);
  provider.engine.stop();
};
 
deploy();
```

Outline of changes:

1.  Update the import to use the newer **@truffle/hdwallet-provider** module.

1.  Update the import to destructure the **abi** (formerly the interface) and the **evm** (bytecode)
    ```const { abi, evm } = require('./compile');```

1.  Pass the **abi** to the contract object:
    ```const result = await new web3.eth.Contract(abi)```

1.  Assign the bytecode to the data property of the deploy method:

a.    .deploy({
b.       data: evm.bytecode.object, 

5.  Call **provider.engine.stop()** to prevent deployment from hanging in the terminal - [Source](https://github.com/trufflesuite/truffle/tree/master/packages/hdwallet-provider#general-usage)

**Completed Code**

Completed project with all changes described above can be found attached to this lecture note as a zip file.

##  Resources for this lecture

---

-   [inbox-update.zip](https://github.com/web3-nfts/bt-web3/raw/main/Curricula/Ethereum-and-Solidity_The_Complete_Developers_Guide/resources/inbox-update.zip)