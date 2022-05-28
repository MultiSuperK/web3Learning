# day4-关于solidity和python间语法差异总结

1. ### mapping

    1. ```solidity
        mapping (address => uint) public accountBalance
        ```

    2. ```python
        accountBalance = {}
        ```

2. ### struct

    1. ```solidity
        struct {
        	string name;
        	uint8 age;
        }
        ```

    2. ```python
        Class Man:
          	name = string()
            age = int()
        ```

        

### solidity三种变量:

1. 状态树变量
    1. 保存到状态树的合约成员变量，消耗gas较高
2. 环境变量
    1. block.timestamp等 合约当前区块的信息
    2. msg.sender; 等 合约调用者的信息
3. 内存变量（局部变量）
    1. 一般在合约的成员函数内部声明
    2. 如计算用的过程变量，临时数据等

### solidity函数修饰符

1. pure，仅计算
2. view，只读
3. public 都可以调用
4. private 仅内部调用
5. internal 自己和子合约可以调用
6. external 仅外部调用

### 一个整型溢出的小demo

```solidity
uint256 b = 260;
uint8 c = uint8(b);

output.c = 4 // 整型溢出
```



### 思考:
​	event 定义的事件是否可以被造假输出