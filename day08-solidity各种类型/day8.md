# 神奇的修饰符



以下代码记录了不同修饰符，对gas的影响

可能跟编译后的操作码有关，需要验证

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;


contract MyContract {
  
  string public text;  // 24519

  function set(string calldata _text) external{
      text = _text;
  }

  function get1() external view returns (string memory _text){
      _text = text;  // 24505
  }

  function get2() external view returns (string memory){
      return text;  // 24593  // 360
  }

  function get3() external view returns (string memory){
      string storage _text = text;
      return _text;  // 24576  //357
  }
}
```





todo: unchecked新特性