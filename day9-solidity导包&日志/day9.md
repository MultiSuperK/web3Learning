# solidity导包&TodoList

```solidity

import './test.sol';  // 全部合约导入

import {Test} from './test.sol';  // 指定合约导入

import {Test as OutTest} from './test.sol';  // 指定合约导入起别名

import {Test as OutTest} from './test.sol';  // 指定合约导入起别名

import {Test as OutTest, Test2 as OutTest2} from './test.sol';  // 指定多个合约导入
```



create2

```sol
function deploySalted(bytes32 salt, uint arg) public{
	D d = new D{salt: salt}(arg);
	ctrAddr = address(d);
}
```



bytes32ToString

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;


contract BytesToString {
    bytes32 public my_string;


    function set(bytes32 s) external returns (bytes32){
        return s;
    }

    function bytes32ToString(bytes32 _bytes32) public pure returns (string memory) {
        uint8 i = 0;
        while(i < 32 && _bytes32[i] != 0) {
            i++;
        }
        bytes memory bytesArray = new bytes(i);
        for (i = 0; i < 32 && _bytes32[i] != 0; i++) {
            bytesArray[i] = _bytes32[i];
        }
        return string(bytesArray);
    }
}
```



### TodoList

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;


contract MyTodoList {

    enum TodoStatus{
        Undo,
        Doing,
        Finish
    }
    
    struct Todo{
        string title;
        TodoStatus status;
    }

    Todo[] public TodoList;

    function create(string calldata _todo_item) external{
        Todo memory _todo = Todo({
            title: _todo_item,
            status: TodoStatus.Undo
        });
        TodoList.push(_todo);
    }

    function retrieve(uint index)external view returns(Todo memory){
        return TodoList[index];
    }

    function update(uint index, uint8 _todo_status) external returns(bool){
        if (_todo_status == 1){
            TodoList[index].status = TodoStatus.Doing;
        } else if (_todo_status == 2){
            TodoList[index].status = TodoStatus.Finish;
        } else {
            TodoList[index].status = TodoStatus.Undo;
        }
        return true;
    }

    function remove(uint index)external{
        delete TodoList[index];
    }

}
```





### 日志

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;


contract MyTodoList {

    event LogMessage(address indexed _from, address indexed _to, string message);

    function sendMessage(address _to, string calldata _message)external{
        emit LogMessage(msg.sender, _to, _message);
    }

}
```

