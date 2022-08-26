# 74. Entering the Lottery

-   Create `Lottery.sol`
    ```
    pragma solidity ^0.4.17;

    contract Lottery {
        address public manager;
        address[] public players;
        
        // function Lottery() public {
        constructor () public {
            manager = msg.sender;
        }
        function enter() public payable {
            require(msg.value > .01 ether);
            players.push(msg.sender);
        }
    }   
    ```

##  Ref
![65. The Lottery Contract](../imgs/65.2_The-Lottery-Contract.png)
---
![](../imgs/22.2_Function-Declarations.png)
---