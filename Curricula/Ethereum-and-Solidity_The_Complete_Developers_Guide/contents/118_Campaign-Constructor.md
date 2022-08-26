# 118. Campaign Constructor

**Campaign.sol** - Constructor
```
pragma solidity ^0.4.17;

contract Campaign {
    address public manager;
    uint public minimumContribution;

    // function Campaign (uint minimum) public {  // Note
    constructor (uint minimum) public {
        manager = msg.sender;
        minimumContribution = minimum;
    }
}
```

Note: `contracts/3_Ballot.sol:7:5: Warning: Defining constructors as functions with the same name as the contract is deprecated. Use "constructor(...) { ... }" instead.
function Campaign (uint minimum) public {
^ (Relevant source part starts here and spans across multiple lines).`