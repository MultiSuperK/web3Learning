# solidity函数编码



### 示例代码

```solidity
// contracts/GLDToken.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;


contract EncodeFunctionCallData{
    function encode(string calldata _fn_calldata) external pure returns (bytes4){
        return bytes4(keccak256(bytes(_fn_calldata)));
        
    }
}


contract TargetContract{
    event Log(bytes _calldata);

    function mockMint(address to, uint amount) external {
        emit Log({
            _calldata:msg.data
        });
    }
}
```



### 目的：

假设我们有一个想调用的nft合约，
但是在项目方前端页面无法调用，被各种前端规则所限制
于是我们便可以尝试使用metamask直接和合约交互，跳过项目方前端限制

### 目标合约分析：

假设目标合约的mint方法是**“mockMint”**

参数1: 0x5B38Da6a701c568545dCfcB03FcB875f56beddC4，

参数2: 11

### 测试：

1: 我们部署TargetContract，模拟调用一下TestFunction.用log在交易中打印msg.data得到:

```bash
0x601904520000000000000000000000005b38da6a701c568545dcfcb03fcb875f56beddc4000000000000000000000000000000000000000000000000000000000000000b
```

2: 部署EncodeFunctionCallData,

调用encode方法得到返回值

```bash
输入: "TestFunction(address,uint256)"
输出: 0x60190452
```

3: 比较后，我们发现得到的bytes4对象，和第一步中的前8位16进制数对应成功

4: 第二个部分0000000000000000000000005b38da6a701c568545dcfcb03fcb875f56beddc4
我们分析可得就是我们第一个地址参数
前面的0是因为adress类型是256个bit，所以，在前面补够64个0

000000000000000000000000000000000000000000000000000000000000000b

第二个实参的值同理，类型因为是uint256,，我们只传了11，所以，不足64的部分全部补0