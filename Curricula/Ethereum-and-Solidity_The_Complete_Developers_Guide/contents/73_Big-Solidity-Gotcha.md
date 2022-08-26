# 73. Big Solidity Gotcha

-   Create `Test.sol`
    ```
    pragma solidity ^0.4.17;

    contract Test {
        string[] public myArray;

        // function Test() public {
        constructor () public {
            myArray.push("hi");
                        
        }

        function getArray() public view returns (string[]) {
            return myArray;
        }
            
    }
    ```

![73. Big Solidity Gotcha](../imgs/73.1_Big-Solidity-Gotcha.png)
---
![73. Big Solidity Gotcha](../imgs/73.2_Big-Solidity-Gotcha.png)
---