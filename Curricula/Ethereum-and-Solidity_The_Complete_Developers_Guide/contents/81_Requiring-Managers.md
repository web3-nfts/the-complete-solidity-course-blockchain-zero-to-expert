#   81. Requiring Managers

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
        function random() private view returns (uint) {
            return uint(keccak256(abi.encodePacked(block.difficulty, now, players)));            
        }
        function pickWinner() public {
            require(msg.sender == manager);

            uint index = random() % players.length;
            // players[index].transfer(this.balance);
            players[index].transfer(address(this).balance);
            players = new address[](0);
        }    
    } 
    ```  


##  Resources for this lecture

---

-   [80-requiring.zip](https://github.com/web3-nfts/bt-web3/raw/main/Curricula/Ethereum-and-Solidity_The_Complete_Developers_Guide/resources/80-requiring.zip)