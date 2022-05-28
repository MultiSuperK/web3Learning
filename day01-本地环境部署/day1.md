# day1

### 安装geth

```sh
brew tap ethereum/ethereum
brew install ethereum
```



设置项目工作文件夹

```bash
mkdir ether-test
cd ether-test
touch gensis.json
mkdir db

tree/ls
```



配置创世文件

```bash
vim gensis.json
粘贴下面内容
{
  "config": {
      "chainId": 987,
      "homesteadBlock": 0,
      "eip155Block": 0,
      "eip158Block": 0
  },
  "difficulty": "0x400",
  "gasLimit": "0x8000000",
  "alloc": {}
}
:wq
待会有个小bug需要自行解决，分叉排序的问题
```



启动geth-Node

cd ether-test

```bash
geth --datadir ./db/ --rpc --rpcaddr=127.0.0.1 --rpcport 8545 --rpccorsdomain "*" \
   --rpcapi "eth,net,web3,personal,admin,shh,txpool,debug,miner" \
   --nodiscover --maxpeers 30 --networkid 198989 --port 30303 \
   --mine --minerthreads 1 \
   --etherbase "0x7df9a875a174b3bc565e6424a0050ebc1b2d1d82" \
   console
```

大概率提示bug

flag provided but not defined: -rpc

版本问题 , 根据geth --help查询新参数后修改如下, 还有sh h写错了

```bash
geth --datadir ./db/ --http --http.addr=127.0.0.1 --http.port 8545 --http.corsdomain "*" \
   --http.api "eth,net,web3,personal,admin,ssh,txpool,debug,miner" \
   --nodiscover --maxpeers 30 --networkid 198989 --port 30303 \
   --mine --miner.threads 1 \
   --miner.etherbase "0x7df9a875a174b3bc565e6424a0050ebc1b2d1d82" \
   console
```



ok,开始运行了，会看到geth已经开挖的很多log



开一个新console，链接geth

```
geth --datadir ./db attach ipc:./db/geth.ipc  // 进入节点交互环境

miner.stop()
miner.start()

```

geth控制台中的其他命令

| 模块     | 解释                                                         |
| -------- | ------------------------------------------------------------ |
| personal | 账户相关操作–新建账户、锁定账户、发送签名交易等。            |
| admin    | 节点管理相关–节点数据存储、网络状况、API 开放状况等。        |
| txpool   | 交易池相关–交易池等待情况，查看某交易详情等。                |
| debug    | 开发调试相关–追踪区块状况，分析区块详情、CPU 状况检测等。    |
| miner    | 挖矿相关–更改奖励收款地址、开启/关闭挖矿功能、设置 gas 费用等。 |
| web3     | 包含了以上模块的总入口，还包含单位换算函数。                 |
| eth      | 提供了操作区块链的相关方法。                                 |
| shh      | 提供了分布式网络 P2P Whisper通信协议的相关方法               |



接收挖矿奖励

```
创建新外部账户
> personal.newAccount('123')  // 密码123
"0xf58b50f5fa5aff09691f2703d76da1195a7eb8b0"  // 账户的私钥
> personal.newAccount('456')  // 密码456
"0xdecaf7b2ee5d6ec74bfb90f2174a56b1c9ed2dc0"

eth.accounts  // 查看账户
web3.fromWei(eth.getBalance(eth.accounts[0]), "ether")  // 查看余额

修改挖矿奖励接受地址
miner.setEtherbase(eth.accounts[0])
```



转账和收款

重启geth-Node，修改etherbase

```

geth --datadir ./db/ --http --http.addr=127.0.0.1 --http.port 8545 --http.corsdomain "*" \
   --http.api "eth,net,web3,personal,admin,ssh,txpool,debug,miner" \
   --nodiscover --maxpeers 30 --networkid 198989 --port 30303 \
   --mine --miner.threads 1 \
   --miner.etherbase "0xf58b50f5fa5aff09691f2703d76da1195a7eb8b0" 
```

进入geth-Node

```sh
geth --datadir ./db attach ipc:./db/geth.ipc
```



不知道为啥，重启后账户没了，本来准备新建两个的(后面发现是启动目录错了，在～目录新建了个db，连gensis文件都没有不知道咋启动的。。。)

#### 尝试解锁

personal.unlockAccount(eth.accounts[0], "123", 300)

#### 报错：

account unlock with HTTP access is forbidden

解决方式:

--allow-insecure-unlock

#### 再次尝试解锁

personal.unlockAccount(eth.accounts[0], "123", 300)

true



```bash
> eth.sendTransaction({from:eth.accounts[0],to:eth.accounts[1],value:web3.toWei(66,'ether')})
"0x864d82b399befb30938ac485b45e5f5fc939dcb529305f4c3308d88770898498"
> web3.fromWei(eth.getBalance(eth.accounts[1]), "ether")
0
> txpool.status
{
  pending: 1,
  queued: 0
}

此时发现我这个1账户并没钱，（之前停止挖矿了）
txpool中查出来有一个打开未处理的交易
>
eth.getTransaction("0x864d82b399befb30938ac485b45e5f5fc939dcb529305f4c3308d88770898498")
{
  blockHash: null,
  blockNumber: null,
  from: "0xf58b50f5fa5aff09691f2703d76da1195a7eb8b0",
  gas: 21000,
  gasPrice: 1000000000,
  hash: "0x864d82b399befb30938ac485b45e5f5fc939dcb529305f4c3308d88770898498",
  input: "0x",
  nonce: 0,
  r: "0x67b2003859a0f0aa4058ebc537863f024b7b79a049f66f7db748d2a185e4f50d",
  s: "0x434d55bfdb45d247d8df6557bc7a9b5db4561b1c0824498ae0cc9a809f33ce3d",
  to: "0xdecaf7b2ee5d6ec74bfb90f2174a56b1c9ed2dc0",
  transactionIndex: null,
  type: "0x0",
  v: "0x7da",
  value: 66000000000000000000
}
```



我们挖一个块再看

```bash
> eth.accounts
["0xf58b50f5fa5aff09691f2703d76da1195a7eb8b0", "0xdecaf7b2ee5d6ec74bfb90f2174a56b1c9ed2dc0"]
> miner.start(1); admin.sleepBlocks(1); miner.stop();
null
> eth.getTransaction("0x864d82b399befb30938ac485b45e5f5fc939dcb529305f4c3308d88770898498")
{
  blockHash: "0x44848b5a6fe584d95e35797b820bb1ad0d25bda09cb4b07ad2886987c358d86a",
  blockNumber: 562,
  from: "0xf58b50f5fa5aff09691f2703d76da1195a7eb8b0",
  gas: 21000,
  gasPrice: 1000000000,
  hash: "0x864d82b399befb30938ac485b45e5f5fc939dcb529305f4c3308d88770898498",
  input: "0x",
  nonce: 0,
  r: "0x67b2003859a0f0aa4058ebc537863f024b7b79a049f66f7db748d2a185e4f50d",
  s: "0x434d55bfdb45d247d8df6557bc7a9b5db4561b1c0824498ae0cc9a809f33ce3d",
  to: "0xdecaf7b2ee5d6ec74bfb90f2174a56b1c9ed2dc0",
  transactionIndex: 0,
  type: "0x0",
  v: "0x7da",
  value: 66000000000000000000
}
> web3.fromWei(eth.getBalance(eth.accounts[1]), "ether")
66
> 哇成功了！！！！
> eth.getBlock(562)
{
  difficulty: 163854,
  extraData: "0xd983010a10846765746888676f312e31372e368664617277696e",
  gasLimit: 77507559,
  gasUsed: 21000,
  hash: "0x44848b5a6fe584d95e35797b820bb1ad0d25bda09cb4b07ad2886987c358d86a",  // 当前区块hash
  logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  miner: "0xf58b50f5fa5aff09691f2703d76da1195a7eb8b0",
  mixHash: "0x2a59ee0eded086f7a13bc32f038fc3779e9e4b1234552b345ee57142a89d2bf2",
  nonce: "0x4a677e930de83e2f",
  number: 562,
  parentHash: "0x9bff491f1172cce3dd353027b865e4349af1d540afcb854dfa01fa3b614dd48a",
  receiptsRoot: "0x6a8711af3acabd398e5291d137ef169d1631c09b389b241533bb77429cc93669",
  sha3Uncles: "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
  size: 655,
  stateRoot: "0x66e6cccf6774ada47b4164a27fbc879d608ddfdc78097db76a215fa3b2e67e88",
  timestamp: 1652002975,
  totalDifficulty: 83369835,
  transactions: ["0x864d82b399befb30938ac485b45e5f5fc939dcb529305f4c3308d88770898498"],  // 刚刚交易的hash
  transactionsRoot: "0x1661b39d80f7e36ae60de1a1daa839ab9c7bcb8c7b02d759afe6c0cc8a813dcc",
  uncles: []
}
```




