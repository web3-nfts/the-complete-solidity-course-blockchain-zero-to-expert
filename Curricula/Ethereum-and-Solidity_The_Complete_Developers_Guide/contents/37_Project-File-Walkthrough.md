#   37. Project File Walkthrough

-  `npm init`

-  setup Project - inbox directory

    ![](../imgs/37.1_Project-File-Walkthrough.png)

- **Create a inbox.sol**
    -   `inbox.sol`
    ```
    pragma solidity ^0.4.17;

    contract Inbox {
        string public message;
        
        function Inbox(string initialMessage) public {
            message = initialMessage;
        }
        
        function setMessage(string newMessage) public {
            message = newMessage;
        }    
        
    }
    ```