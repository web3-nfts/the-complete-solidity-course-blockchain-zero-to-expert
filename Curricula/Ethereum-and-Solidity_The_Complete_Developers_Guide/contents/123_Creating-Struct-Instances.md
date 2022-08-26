# 123. Creating Struct Instances

**Campaign.sol** - Creating Struct Instances
```
pragma solidity ^0.4.17;

contract Campaign {
    struct Request {
        string description;
        uint value;
        address recipient;
        bool complete;
    }

    Request [] public requests;
    address public manager;
    uint public minimumContribution;
    address[] public approvers;

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

        approvers.push(msg.sender);
    }

    function createRequest(string description, uint value, address recipient) public restricted {
        Request newRequest = Request ({
            description: description,
            value: value,
            recipient: recipient
            
        });

        requests.push(newRequest) ;
    }
}
```

<details>
  <summary>Compile - issues</summary>

![123. Creating Struct Instances](../imgs/123.0_Creating-Struct-Instances.png)
---
**contracts/3_Ballot.sol:33:9: Warning:**
```
contracts/3_Ballot.sol:33:9: Warning: Variable is declared as a storage pointer. Use an explicit "storage" keyword to silence this warning.
Request newRequest = Request ({
^----------------^
```
**contracts/3_Ballot.sol:33:30: TypeError:**
```
contracts/3_Ballot.sol:33:30: TypeError: Wrong argument count for struct constructor: 3 arguments given but expected 4.
Request newRequest = Request ({
^ (Relevant source part starts here and spans across multiple lines).
```
**contracts/3_Ballot.sol:33:9: TypeError:**
```
contracts/3_Ballot.sol:33:9: TypeError: Type struct Campaign.Request memory is not implicitly convertible to expected type struct Campaign.Request storage pointer.
Request newRequest = Request ({
^ (Relevant source part starts here and spans across multiple lines).
```

- [What does the keyword "memory" do exactly?](https://ethereum.stackexchange.com/questions/1701/what-does-the-keyword-memory-do-exactly)
</details>  
