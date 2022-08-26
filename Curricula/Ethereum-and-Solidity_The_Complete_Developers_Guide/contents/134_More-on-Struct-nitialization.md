# 134. More on Struct Initialization

**Campaign.sol** - More on Struct Initialization
```
pragma solidity ^0.4.17;

contract Campaign {
    struct Request {
        string description;
        uint value;
        address recipient;
        bool complete;
        uint approvalCount;
        mapping(address => bool) approvals;
    }

    Request [] public requests;
    address public manager;
    uint public minimumContribution;
    mapping(address => bool) public approvers;

    modifier restricted() {
        require(msg.sender == manager);
        _;
    }

    constructor (uint minimum) public {    
        manager = msg.sender;
        minimumContribution = minimum;
    }

    function contribute() public payable {
        require (msg.value > minimumContribution);

        approvers[msg.sender] = true;
    }

    function createRequest(string description, uint value, address recipient) public restricted {
        Request memory newRequest = Request ({
            description: description,
            value: value,
            recipient: recipient,
            complete: false,
            approvalCount: 0            
        });

        requests.push(newRequest) ;
    }
}
```

![115. Fixing Kickstarter's Issues](../imgs/115.3_Fixing-Kickstarter's-Issues.png)
---
![115. Fixing Kickstarter's Issues](../imgs/115.4_Fixing-Kickstarter's-Issues.png)
---