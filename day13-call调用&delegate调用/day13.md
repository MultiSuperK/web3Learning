# solidity call与delegateCall

### 低级call调用

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

contract TetsCall {

    string public message;
    uint public x;

    event Log(string message);

    fallback() payable external{
        emit Log("fallback was called");
    }

    function foo(string memory _message,uint _x) external payable returns(bool,uint) {
        message = _message;
        _x = x;
        return (true,99);
    }
}

contract CallFunction {
    bytes public data;
    event Logs(uint gasUse);

    function testFunction(address _test) external payable{  //121505
        uint gas0 = gasleft();
        emit Logs(gas0);    //117896
        emit Logs(msg.value);   //msg.value为部署合约时传入的eth
        (bool success,bytes memory _data) = _test.call{value:msg.value,gas:32000}  (abi.encodeWithSignature("foo(string,uint256)","test",123));
        emit Logs(gas0-gasleft());  //31365
        require(success,"call failed");
        data = _data;
    } //此处gas给的过少会报错，如果不给则以为剩余gas全给；

    function testDoesExist(address _test) external {
        (bool success,) = _test.call(abi.encodeWithSignature("DoesNotExist(string,uint)","test",123));
        require(success,"call failed");

    }
}	//调用不存在的函数会报错，无返回的data
```



### delegateCall

说明: 

​	与call不同的是，合约代码使用delegatecall调用其他合约代码，状态变量以及<u>*上下文还是本合约*</u>
call是直接去*<u>其他合约**环境**</u>*中调用，调用使用的环境以及状态变量都是**其他环境**的，
钱也会打到call的合约～

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

contract BasicContract {

    uint public number;
    address public sender;
    uint public value;

    function setNumber(uint _number) external payable{
        number = _number;
        sender = msg.sender;
        value = msg.value;
    }
}

contract BizContract {
    uint public number;
    address public sender;
    uint public value;

    function setNumber(address _delegator,uint _number) external payable{
        (bool success, ) = _delegator.delegatecall(
            // abi.encodeWithSignature("setNumber(uint256)", _number);
            abi.encodeWithSelector(BasicContract.setNumber.selector, _number)
        );
        require(success, "delegate successed");
    }
}
```

