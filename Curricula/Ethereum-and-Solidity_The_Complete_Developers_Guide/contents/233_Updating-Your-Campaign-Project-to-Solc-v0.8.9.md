# 233. Updating Your Campaign Project to Solc v0.8.9

This lecture will walk you through all of the changes needed to bring your project up to date with the latest Solc v0.8.9.

**Environment Setup**

Due to expected dependency conflicts with old installed versions, it would be best to create a brand new project.

In your terminal of choice, run the following:

1. mkdir kickstart-updated
    ```
    mkdir kickstart-updated
    ```

1.  cd kickstart-updated
    ```
    cd kickstart-updated
    ```

1.  npm init -y
    ```
    npm init -y
    ```

1.  npm install solc@0.8.9 web3 mocha ganache-cli @truffle/hdwallet-provider fs-extra
    ```
    npm install solc@0.8.9 web3 mocha ganache-cli @truffle/hdwallet-provider fs-extra
    ```

1.  npm install next react react-dom semantic-ui-css semantic-ui-react
    ```
    npm install next react react-dom semantic-ui-css semantic-ui-react
    ```

1.  npm install next-routes --legacy-peer-deps
    ```
    npm install next-routes --legacy-peer-deps
    ```

1.  Update your scripts in the package.json file:
    ```
    "scripts": {
        "test": "mocha",
        "dev": "node server.js"
    },
    ```
1.  Copy your **components, ethereum, pages, test, server.js, routes.js**, and **.gitignore** files and folders into the new kickstart-updated project directory.

Here is the package.json file for reference (your versions should not be lower than those specified below):
```
{
    "name": "kickstart-updated",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "test": "mocha",
        "dev": "node server.js"
    },
    "author": "",
    "license": "ISC",
    "dependencies": {
        "@truffle/hdwallet-provider": "^2.0.11",
        "fs-extra": "^10.1.0",
        "ganache-cli": "^6.12.2",
        "mocha": "^10.0.0",
        "next": "^12.2.2",
        "next-routes": "^1.4.2",
        "react": "^18.2.0",
        "react-dom": "^18.2.0",
        "semantic-ui-css": "^2.4.1",
        "semantic-ui-react": "^2.1.3",
        "solc": "^0.8.15",
        "web3": "^1.7.4"
    }
}
```

**Campaign.sol**
```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.9;

contract CampaignFactory {
    address payable[] public deployedCampaigns;

    function createCampaign(uint minimum) public {
        address newCampaign = address(new Campaign(minimum, msg.sender));
        deployedCampaigns.push(payable(newCampaign));
    }

    function getDeployedCampaigns() public view returns (address payable[] memory) {
        return deployedCampaigns;
    }
}

contract Campaign {
    struct Request {
        string description;
        uint value;
        address recipient;
        bool complete;
        uint approvalCount;
        mapping(address => bool) approvals;
    }

    Request[] public requests;
    address public manager;
    uint public minimumContribution;
    mapping(address => bool) public approvers;
    uint public approversCount;

    modifier restricted() {
        require(msg.sender == manager);
        _;
    }

    constructor (uint minimum, address creator) {
        manager = creator;
        minimumContribution = minimum;
    }

    function contribute() public payable {
        require(msg.value > minimumContribution);

        approvers[msg.sender] = true;
        approversCount++;
    }

    function createRequest(string memory description, uint value, address recipient) public restricted {
        Request storage newRequest = requests.push(); 
        newRequest.description = description;
        newRequest.value= value;
        newRequest.recipient= recipient;
        newRequest.complete= false;
        newRequest.approvalCount= 0;
    }

    function approveRequest(uint index) public {
        Request storage request = requests[index];

        require(approvers[msg.sender]);
        require(!request.approvals[msg.sender]);

        request.approvals[msg.sender] = true;
        request.approvalCount++;
    }

    function finalizeRequest(uint index) public restricted {
        Request storage request = requests[index];

        require(request.approvalCount > (approversCount / 2));
        require(!request.complete);

        payable(request.recipient).transfer(request.value);
        request.complete = true;
    }
    
    function getSummary() public view returns (
    uint, uint, uint, uint, address
    ) {
        return (
        minimumContribution,
        address(this).balance,
        requests.length,
        approversCount,
        manager
        );
    }
    
    function getRequestsCount() public view returns (uint) {
        return requests.length;
    }
}   
```

Outline of changes:

1.  Update the pragma version at the top of the contract file to **^0.8.9**

1.  Add an SPDX identifier to the top of the contract (will address compilation warnings) - [Source.](https://docs.soliditylang.org/en/v0.8.9/layout-of-source-files.html)

1.  Refactor the constructor to use the new **constructor** keyword - [Source.](https://docs.soliditylang.org/en/v0.8.9/050-breaking-changes.html)

1.  Remove the **public** keyword from the constructor - [Source.](https://docs.soliditylang.org/en/v0.8.9/070-breaking-changes.html?highlight=constructor)

1.  Update all instances of **address[]** to the new **address payable[]** type - [Source.](https://docs.soliditylang.org/en/latest/050-breaking-changes.html#explicitness-requirements)

1.  Specify the data location of address payable[] in the getDeployedCampaigns function to be **memory** - [Source.](https://docs.soliditylang.org/en/v0.8.9/050-breaking-changes.html#explicitness-requirements)

1.  Specify the data location of string description in the createRequest function to be **memory** - [Source.](https://docs.soliditylang.org/en/v0.8.9/050-breaking-changes.html#explicitness-requirements)

1.  In the createCampaign function, explicitly convert the contract to type address:
`address(new Campaign(minimum, msg.sender));` - [Source.](https://docs.soliditylang.org/en/v0.8.9/050-breaking-changes.html#explicitness-requirements)

1.  In the createCampaign function, make the newCampaign payable:
`deployedCampaigns.push(payable(newCampaign));` - [Source](https://docs.soliditylang.org/en/v0.8.13/080-breaking-changes.html#new-restrictions)

1.  In the createRequest function, refactor the Struct mapping:
    ```
    Request storage newRequest = requests.push(); 
        newRequest.description = description;
        newRequest.value= value;
        newRequest.recipient= recipient;
        newRequest.complete= false;
        newRequest.approvalCount= 0;
    ```
    - [Source](https://docs.soliditylang.org/en/v0.7.1/070-breaking-changes.html#mappings-outside-storage)

1.  In the getSummary function, explicitly convert contract type to address: **address(this).balance** - [Source](https://docs.soliditylang.org/en/v0.8.11/050-breaking-changes.html).

1.  In the finalizeRequest function, explicitly convert request.recipient to type address payable: **payable(request.recipient)** - [Source](https://docs.soliditylang.org/en/v0.8.11/080-breaking-changes.html#new-restrictions).

**compile.js**
```
const path = require("path");
const solc = require("solc");
const fs = require("fs-extra");

const buildPath = path.resolve(__dirname, "build");
fs.removeSync(buildPath);

const campaignPath = path.resolve(__dirname, "contracts", "Campaign.sol");
const source = fs.readFileSync(campaignPath, "utf8");

const input = {
    language: "Solidity",
    sources: {
        "Campaign.sol": {
        content: source,
        },
    },
    settings: {
        outputSelection: {
        "*": {
            "*": ["*"],
        },
        },
    },
};

const output = JSON.parse(solc.compile(JSON.stringify(input))).contracts[
"Campaign.sol"
    ];
    
    fs.ensureDirSync(buildPath);
    
    for (let contract in output) {
    fs.outputJsonSync(
        path.resolve(buildPath, contract.replace(":", "") + ".json"),
        output[contract]
    );
}
```
Outline of changes:

1.  Add the expected JSON formatted input, specifying the language, sources, and outputSelection - [Source](https://docs.soliditylang.org/en/v0.8.7/using-the-compiler.html#compiler-input-and-output-json-description).

1.  Update the output to provide the expected JSON formatted output - [Source](https://github.com/ethereum/solc-js#example-usage-without-the-import-callback)

**Campaign.test.js**
```
const assert = require("assert");
const ganache = require("ganache-cli");
const Web3 = require("web3");
const web3 = new Web3(ganache.provider());
 
const compiledFactory = require("../ethereum/build/CampaignFactory.json");
const compiledCampaign = require("../ethereum/build/Campaign.json");
 
let accounts;
let factory;
let campaignAddress;
let campaign;
 
beforeEach(async () => {
  accounts = await web3.eth.getAccounts();
 
  factory = await new web3.eth.Contract(compiledFactory.abi)
    .deploy({ data: compiledFactory.evm.bytecode.object })
    .send({ from: accounts[0], gas: "1400000" });
 
  await factory.methods.createCampaign("100").send({
    from: accounts[0],
    gas: "1000000",
  });
 
  [campaignAddress] = await factory.methods.getDeployedCampaigns().call();
  campaign = await new web3.eth.Contract(compiledCampaign.abi, campaignAddress);
});
 
describe("Campaigns", () => {
  it("deploys a factory and a campaign", () => {
    assert.ok(factory.options.address);
    assert.ok(campaign.options.address);
  });
 
  it("marks caller as the campaign manager", async () => {
    const manager = await campaign.methods.manager().call();
    assert.equal(accounts[0], manager);
  });
 
  it("allows people to contribute money and marks them as approvers", async () => {
    await campaign.methods.contribute().send({
      value: "200",
      from: accounts[1],
    });
    const isContributor = await campaign.methods.approvers(accounts[1]).call();
    assert(isContributor);
  });
 
  it("requires a minimum contribution", async () => {
    try {
      await campaign.methods.contribute().send({
        value: "5",
        from: accounts[1],
      });
      assert(false);
    } catch (err) {
      assert(err);
    }
  });
 
  it("allows a manager to make a payment request", async () => {
    await campaign.methods
      .createRequest("Buy batteries", "100", accounts[1])
      .send({
        from: accounts[0],
        gas: "1000000",
      });
    const request = await campaign.methods.requests(0).call();
 
    assert.equal("Buy batteries", request.description);
  });
 
  it("processes requests", async () => {
    await campaign.methods.contribute().send({
      from: accounts[0],
      value: web3.utils.toWei("10", "ether"),
    });
 
    await campaign.methods
      .createRequest("A", web3.utils.toWei("5", "ether"), accounts[1])
      .send({ from: accounts[0], gas: "1000000" });
 
    await campaign.methods.approveRequest(0).send({
      from: accounts[0],
      gas: "1000000",
    });
 
    await campaign.methods.finalizeRequest(0).send({
      from: accounts[0],
      gas: "1000000",
    });
 
    let balance = await web3.eth.getBalance(accounts[1]);
    balance = web3.utils.fromWei(balance, "ether");
    balance = parseFloat(balance);
    assert(balance > 104);
  });
});
```

Outline of changes:

1.  Find all instances of **JSON.parse(compiledFactory.interface)** and replace with **compiledFactory.abi**

1.  Find **compiledFactory.bytecode** in the deploy method and replace it with **compiledFactory.evm.bytecode.object**

1.  In the send method of the factory, change the gas limit from **1000000** to **1400000**:

```
.send({ from: accounts[0], gas: "1400000" });
```

**deploy.js**
```
const HDWalletProvider = require("@truffle/hdwallet-provider");
const Web3 = require("web3");
const compiledFactory = require("./build/CampaignFactory.json");
 
const provider = new HDWalletProvider(
  "YOUR_MNEMONIC",
  // remember to change this to your own phrase!
  "YOUR_INFURA_URL"
  // remember to change this to your own endpoint!
);
const web3 = new Web3(provider);
 
const deploy = async () => {
  const accounts = await web3.eth.getAccounts();
 
  console.log("Attempting to deploy from account", accounts[0]);
 
  const result = await new web3.eth.Contract(compiledFactory.abi)
    .deploy({ data: compiledFactory.evm.bytecode.object })
    .send({ gas: "1400000", from: accounts[0] });
 
  console.log("Contract deployed to", result.options.address);
  provider.engine.stop();
};
deploy();
```

Outline of changes:

1.  Find **JSON.parse(compiledFactory.interface)** and replace with **compiledFactory.abi**

1.  Find **compiledFactory.bytecode** in the deploy method and replace it with **compiledFactory.evm.bytecode.object**

1.  In the send method of the factory, change the gas limit from **1000000** to **1400000**:
    ```
    .send({ from: accounts[0], gas: "1400000" });
    ```
1.  Call **provider.engine.stop()** to prevent deployment from hanging in the terminal - [Source](https://github.com/trufflesuite/truffle/tree/master/packages/hdwallet-provider#general-usage)

**factory.js**
```
import web3 from "./web3";
import CampaignFactory from "./build/CampaignFactory.json";
 
const instance = new web3.eth.Contract(
  CampaignFactory.abi,
  "0x74d6c5f4426DfCE08e4C5019B832C7c4f13c45bE"
);
 
export default instance;
```

Outlines of changes:

1.  Find **JSON.parse(CampaignFactory.interface)** and replace with **CampaignFactory.abi**

**campaign.js**
```
import web3 from "./web3";
import Campaign from "./build/Campaign.json";
 
const campaign = (address) => {
  return new web3.eth.Contract(Campaign.abi, address);
};
export default campaign;
```

Outlines of changes:

1.  Find **JSON.parse(Campaign.interface)** and replace with **Campaign.abi**

**Important**
After performing all of the updates and changes shown in this note, make sure you deploy a brand new contract. Then, update the factory.js module with this newly deployed contract address.

**Completed Code**

Completed project with all changes described above can be found attached to this lecture note as a zip file. If using, you must run the install command with the legacy-peer-deps flag!
```
npm install --legacy-peer-deps
```


##  Resources for this lecture

---

-   [kickstart-updated.zip](https://beatlesm.s3.us-west-1.amazonaws.com/ethereum-and-solidity-complete-developer-guide/kickstart-updated.zip)