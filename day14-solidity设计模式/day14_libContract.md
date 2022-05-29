```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

library ArrayBase{

    function find(uint[] memory array,uint value) internal pure returns (uint index){

        uint _length = array.length;

        for (uint i; i < _length; ){
            if (array[i] == value){
                return i;
            }
            ++i;
        }

        revert('not found');
    }
}


contract BizContract{
    using ArrayBase for uint[];
    uint[] public biz_arr = [9, 5, 2, 7];

    function test_arr_find(uint value)public view returns(uint){
        return biz_arr.find(value);
    }
}
```