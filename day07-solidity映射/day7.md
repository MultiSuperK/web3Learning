# solidity映射



### 映射声明

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

contract ArrayTest{
    mapping(address => uint) public account_salary;

    function set() external returns (bool){
        account_salary[msg.sender] += 10;
        return true;
    }

    function get() external view returns (uint){
        return account_salary[msg.sender];
    }
}
```



映射迭代

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

contract Dict{
    // 有序字典
    struct AddressMap{
        mapping(address => uint) items;
        address[] keys;
    }

    address public owner;
    AddressMap private address_map;

    constructor(){
        owner = msg.sender;
        address_map.keys.push(owner);
        address_map.items[owner] = 10 ** 18;
    }

    function set(uint value) external returns (bool){
        address_map.items[msg.sender] = value;
        address_map.keys.push(msg.sender);
        return true;
    }

    function get() external view returns (uint){
        return address_map.items[msg.sender];
    }

    function count() external view returns (uint){
        assert(msg.sender == owner);
        return address_map.keys.length;
    }
}
```



### CarNft

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

contract CarNFT{
    
    struct Car{
        uint id;
        string color;
    }

    // 油漆工厂
    string[] private Colors;
    
    // 可以被mint的车
    Car[] public OnSaleCars;

    uint public OnSaleIndex;

    uint public saleBeginTime;
    uint public saleEndTime;

    // 用户车量对照
    mapping(address => Car) CarStock;
    mapping(address => bool) CarState;

    // 
    constructor(uint car_count){
        saleBeginTime = block.timestamp + 3600 * 24 * 7;
        saleEndTime = block.timestamp + 3600 * 24 * 8;

        Colors = ["black", "white", "yellow"];
        for (uint i; i < car_count; i++){
            Car memory _car = Car({id:i + 1, color:Colors[i % Colors.length]});
            OnSaleCars.push(_car);
        } 
    }

    modifier onSaleAndNoMint(){
        require(OnSaleIndex < OnSaleCars.length, "sale off");
        require(!CarState[msg.sender]);
        require(block.timestamp >= saleBeginTime && block.timestamp <= saleEndTime);
        _;
    }

    function mint() external onSaleAndNoMint returns(Car memory _car){
        _car = OnSaleCars[OnSaleIndex];
        CarStock[msg.sender] = _car;
        CarState[msg.sender] = true;
        OnSaleIndex += 1;
    }

    function Unlock() external {
        saleEndTime += 3600 * 24 * 3;
    }

}
```





### Enum枚举

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

contract EnumTest{
    enum Status{
        None,
        Ready,
        Executing,
        End,
        Fail,
        Fault
    }

    Status public status;

    function set(Status _status)external{
        status = _status;
    }

    function get()external view returns(Status){
        return status;
    }

    function execute()external returns(Status){
        status = Status.Executing;
        return status;
    }

    function reset()external{
        delete status;
    }

}
```





### CallData

```solidity
function fn(uint calldata value) external returns (uint)
```

