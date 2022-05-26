# day3
# https://ethbook.abyteahead.com/ch7/index.html

#### 只读操作

不会修改区块链状态，在调用合约的时候也不会触发任何状态变更，速度较快；

#### 写操作

会修改区块链状态，会消耗gas，因为更改了某些账户的存储空间；

#### 存储空间

链上的数据需要被全球各地的节点储存，所以消耗gas意味着状态变量改变，也意味着需要消耗存储空间，需要支付eth作为消耗；

![image-20220525171905575](/Users/gamer9527/Library/Application Support/typora-user-images/image-20220525171905575.png)

#### 虚拟机执行资源

![image-20220525171949572](/Users/gamer9527/Library/Application Support/typora-user-images/image-20220525171949572.png)

除开以上的三种还有calldata的概念，同属于内存中，但是属于不可更改的变量；

#### 合约调用

常见的可以参考safemath合约，通常被合约调用来验证整型溢出等；其中msg.sender属于合约调用者，执行完成后可以通过return返回调用结果至调用方的内存区；

同时省gas小窍门：合约可以讲safemath内置于合约内部功能，可以节省每次要import safemath合约，减少了调用次数，节省了在其他evm中跑safemath的gas费用；在于在code篇幅和gas之间做取舍；

#### 示意图

![image-20220525172523614](/Users/gamer9527/Library/Application Support/typora-user-images/image-20220525172523614.png)

#### gas limit的作用

举例：在metamask中可以设定gaslimit和gas单价(Wei,Gwei),实际花费的gas和gas单价即为该笔交易付出的gas fee，所以如果不设置gas limit，一旦合约限于无限循环就会无限执行，同时也会无限消耗gas；![image-20220525172756699](/Users/gamer9527/Library/Application Support/typora-user-images/image-20220525172756699.png)

![image-20220525172830267](/Users/gamer9527/Library/Application Support/typora-user-images/image-20220525172830267.png)

当一笔交易执行失败的时候，并不会消耗全部gas，同时合约也不会继续执行，大部分情况如果用require判断是会退回剩余gas，而assert相对比较严格，会扣除所有gas(也许可以用来防科学家)；



