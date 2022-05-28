# 合约继承与作用域

#### 如何继承，以及复用代码，以及各种继承方式的代码作用域



### 单合约继承

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;


contract Animal{

    bool public life = true;

    function eat()external virtual returns(string memory){
        return "they eat directely";
    }

    function died()external virtual{
        require(!!life, "its died");
    }

    function kill()external virtual{
        life = false;
    }
}


contract Human  is Animal {

    function eat()external override returns(string memory){
        return "they eat by tools";
    }
}
```



### 多合约继承

原则: **从基础到高级的顺序进行合约继承**

```solidity
contract Man is Animal, Human{
    function eat() external override(Animal, Human) pure returns(string memory){
        return "man eat very fast";
    }
}
```



```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;


contract X{
    uint public bit_poll;
    constructor(uint value){
        bit_poll = value;
    }
}


contract Y{
    uint public bit_poll2;
    constructor(uint value){
        bit_poll2 = value;
    }
}


contract V1 is X(10){
}


contract V2 is X{

    constructor(uint value) X(value){
        bit_poll = value;
    }
}

contract V3 is X(10), Y{

    constructor(uint value) Y(value){
    }
}
```





### 继承重写父类方法

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;


contract X{
    uint public bit_poll;
    event Log(string msg);

    constructor(uint value){
        bit_poll = value;
    }

    function fn()public virtual returns(string memory){
        return "x.fn";
    }
}


contract Y is X{
    constructor(uint value) X(2*value){
    }

    function fn()public override returns(string memory response){
        emit Log("y.fn");
        response = X.fn();
    }
}
```





### 父类调用

#### super.fater_function时，会把最后继承的父合约的同名函数

如果父合约同名函数还有其他嵌套调用怎继续调用





1. #### private  进本合约内调用

2. #### internal  在private基础上，可被子合约调用

3. #### public  所有都可调用

4. #### external  仅用于函数修饰，只有外部可以调用