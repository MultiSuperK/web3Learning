# solidity支付相关



### 不可变量

immutable 修饰符



### 接受发送主币 

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;


contract PayToTest{
    constructor () payable{
    }

    receive () external payable {}

    function sendByTransfer(address payable to, uint value) external payable {
        to.transfer(value); // 33546
        // 2258
    }

    function sendBySend(address payable to) external payable {
        bool send_ret = to.send(123);  //33257
        // 2258
        require(send_ret, "send faild");
    }

    function sendByCall(address payable to) external payable {
        (bool call_ret, ) = to.call{value: 123}("");  // 33485
        // 6980
        require(call_ret, "call faild");
    }
}

contract Receiver{
    event Log(address from, uint value, uint gas);

    receive() external payable {
        emit Log(msg.sender, msg.value, gasleft());
    }
}
```



### 跑路合约

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;


contract MyWallet{
    address payable public owner;
    constructor() payable{
        owner = payable(msg.sender);
    }

    receive()external payable{}

    function rug(uint amount) external {
        assert(msg.sender == owner);
        payable(msg.sender).transfer(amount);
    }

    function getBalance() external view returns(uint){
        return address(this).balance;
    }
}
```





### 调用其他合约

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;


contract ProxyMain{
    function setValue(Main main, uint value)external{
        main.setValue(value);
    }

    function getValue(Main main)external view returns(uint){
        return main.getValue();
    }
}


contract Main{
    uint public balance;

    function setValue(uint value)external{
        balance += value;
    }

    function getValue()external view returns(uint){
        return balance;
    }
}
```



### 接口合约

base

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.11;

contract BaseCounter {

    uint256 count;
    event Log(address _sender);

    function set(uint256 value) public {
        count = value;
    }

    function get() public returns (uint256){
        emit Log(msg.sender);
        return count;
    }

    function inc() external {
        count += 1;
    }

    function dec() external {
        count -= 1;
    }
}
```



proxy

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.11;


interface ICounter{
    function inc() external;
    function get() external returns (uint);
    function set(uint value) external;
}


contract MyCounter{

    event Log(address _sender);
    function biz(address ctr) external{
        ICounter(ctr).inc();
    }

    function set(address ctr, uint value) external {
        ICounter(ctr).set(value);
    }

    function get(address ctr) external returns(uint){
        emit Log(msg.sender);
        return ICounter(ctr).get();
    }
}
```

