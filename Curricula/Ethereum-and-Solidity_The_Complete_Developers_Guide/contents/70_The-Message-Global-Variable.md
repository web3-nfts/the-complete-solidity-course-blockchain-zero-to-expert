#   70. The Message Global Variable

-   Create `Lottery.sol`
    ```
    pragma solidity ^0.4.17;

    contract Lottery {
        address public manager;
        
        function Lottery() public {
            manager = msg.sender;
        }
    }
    ```

---

![70. The Message Global Variable](../imgs/70.1_The-Message-Global-Variable.png)
---
![70. The Message Global Variable](../imgs/70.2_The-Message-Global-Variable.png)
---




##  Resources for this lecture

---

-   [69-message.zip](https://github.com/web3-nfts/bt-web3/raw/main/Curricula/Ethereum-and-Solidity_The_Complete_Developers_Guide/resources/69-message.zip)