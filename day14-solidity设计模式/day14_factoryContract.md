```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

contract MyAccount {

    address public bank;
    address public owner;

    constructor(address _owner) payable {
        owner = _owner;
        bank = msg.sender;
    }
}


contract MyAccountFactory {

    MyAccount[] public accounts;

    // 方便工厂升级时，把旧用户迁移过来
    constructor(MyAccount[] memory _accounts){
        for (uint i; i < _accounts.length; ++i){
            accounts.push(_accounts[i]);
        }
    }

    // 业务
    function creatNewAccount(address _owner) external payable {
        MyAccount _account = new MyAccount{value: msg.value}(_owner);
        accounts.push(_account);
    }

    function getBalance(address _account) external view returns (uint){
        return _account.balance;
    }
}
```