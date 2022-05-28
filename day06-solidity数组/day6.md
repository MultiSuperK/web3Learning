# day6-solidity数组



### 01-数组基本操作

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

contract ArrayTest{
    uint[] public dynamicNums = [1,2];
    uint[3] public constNums = [1,2,3];

    function main() external {
        dynamicNums.push(3);
        dynamicNums.push(4);
        dynamicNums.push(5); // [1,2,3,4,5]

        dynamicNums.pop();  // [1,2,3,4]
        delete dynamicNums[dynamicNums.length - 1];  // [1,2,3,0]
    }

    function returnArr() external view returns(uint[] memory, uint[] memory){
        uint[] memory mNums = new uint[](3);
        mNums[0] = 1;
        mNums[1] = 2;
        mNums[2] = 3;
        delete mNums[2];  // 允许置0
        // mNums.pop();  // 不允许pop和push
        return (dynamicNums, mNums);
    }
}
```