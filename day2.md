



# web3学习第2天

今天主要目标是部署智能合约

会记录一些智能合约部署时遇到的bug

学习资料https://ethbook.abyteahead.com/ch6/install.html

### 正文

1. ```shell
   安装solidity
   brew tap ethereum/ethereum
   brew install solidity
   solc --version
   ```

2. ```shell
   编写第一份合约代码//path: 
   vim Vault.sol
   pragma solidity ^0.8.13;
   
   contract Vault {
       uint vaultData;
       function set(uint data) public{
           vaultData = data;
       }
   
       function get() public view returns (uint) {
           return vaultData;
       }
   }
   # public view 是solidity中的一个只读修饰符
   ```

3. ```shell
   编译成abi&bin
   solc --optimize --combined-json abi,bin Vault.sol
   out:
   {
     "contracts": {
       "Vault.sol:Vault": {
         "abi": [
           {
             "inputs": [],
             "name": "get",
             "outputs": [
               {
                 "internalType": "uint256",
                 "name": "",
                 "type": "uint256"
               }
             ],
             "stateMutability": "view",
             "type": "function"
           },
           {
             "inputs": [
               {
                 "internalType": "uint256",
                 "name": "data",
                 "type": "uint256"
               }
             ],
             "name": "set",
             "outputs": [],
             "stateMutability": "nonpayable",
             "type": "function"
           }
         ],
         "bin": "6080604052348015600f57600080fd5b5060ac8061001e6000396000f3fe6080604052348015600f57600080fd5b506004361060325760003560e01c806360fe47b11460375780636d4ce63c146049575b600080fd5b60476042366004605e565b600055565b005b60005460405190815260200160405180910390f35b600060208284031215606f57600080fd5b503591905056fea2646970667358221220b06823f5745d35f6194605d5eae4310d45b3820e610eea150ccc356d79e09ba964736f6c634300080d0033"
       }
     },
     "version": "0.8.13+commit.abaa5c0e.Darwin.appleclang"
   }
   ```

4. 以下是abi的阅读参照，接口作用解释

5. | 名称            | 解释                                                       |
   | --------------- | ---------------------------------------------------------- |
   | type            | 接口类型，默认为function，也可以是construnctor、fallback等 |
   | name            | 方法名字                                                   |
   | inputs          | 接口输入参数列表，每一项都是参数名+参数类型                |
   | outputs         | 接口输出结果列表，每一项都是返回值名+返回值类型            |
   | constant        | 布尔值，若为true 则该接口不修改合约存储区，是只读方法      |
   | payable         | 布尔值，标明该方法是否接受以太币                           |
   | stateMutability | 枚举类型，为下列选项之一：                                 |
   | pure            | 表明该方法只读不修改存储，且不读取区块链状态               |
   | view            | 表明该方法只读不修改存储，但读取区块链状态                 |
   | nonpayable      | 该方法不能接受以太币                                       |
   | payable         | 该方法可以接受以太                                         |

6. ```shell
   # 另存到js，待会方便在geth客户端用于部署
   vim temp.js
   var output = "刚刚得到的abi描述文本"  # 注意这份文件被加载后会在全局声明一个output对象
   ```

7. ```javascript
   // 在geth客户端节点加载合约文件
   loadScript('～/yourpath/ether-test/temp.js')  // 有一个output
   
   > var myAbi = output.contracts['Vault.sol:Vault'].abi
   undefined
   > var myContract = eth.contract(myAbi)  // 这一步原始文档上有一个小bug，序列化问题
   undefined
   > var myBinCode = "0x" + output.contracts['Vault.sol:Vault'].bin
   undefined
   ```

8. ```shell
   # 部署合约，解锁
   personal.unlockAccount(eth.accounts[0], '123', 300)
   > var deployObject = {from: eth.accounts[0], data: myBinCode, gas: 1000000 }
   undefined
   > var myContractInstance = myContract.new(deployObject)  # 使用abi建立连接
   undefined
   > myContractInstance
   {
     abi: [{
         inputs: [],
         name: "get",
         outputs: [{...}],
         stateMutability: "view",
         type: "function"
     }, {
         inputs: [{...}],
         name: "set",
         outputs: [],
         stateMutability: "nonpayable",
         type: "function"
     }],
     address: "0x029bff382c398323583ad6420d1913ccfbba0513",
     transactionHash: "0xc1f25dc58a3143e4ab64a5595daa61e65f5a24efcef7c5b85180c5966ba0bc2a",
     allEvents: function bound(),
     get: function bound(),
     set: function bound()
   }
   ```

9. ```javascript
   myContractInstance.get.call()
   大概率报错
   Error: Returned error: invalid opcode: SHR
   
   // 解决方式(可以自行去了解下SHR指令的历史，来自下面两个分叉)
   停止你的geth矿工节点，在gensis.json中加入两个初始区块
   "byzantiumBlock": 0,
   "constantinopleBlock": 0
   重新初始化创世区块
   geth --datadir "./db" init gensis.json
   重新启动geth私链主节点
   ```

10. ```shell
   # 如下，即可调用成功
   > myContractInstance.set.sendTransaction(17, { from: eth.accounts[0], gas: 1000000})
   "0xd24802f473be45792df73eb2d9c17b87b1bd4f08ca30db35bc44b3c8cb0073c0"
   > myContractInstance.get.call()
   17
   ```



#### 总结:

我们今天完成了第一份智能合约的部署，并且解决了2个小bug，一个跟历史分叉相关，一个跟json的反序列化相关。

下一次将会进行evm学习记录和solidity语法练习