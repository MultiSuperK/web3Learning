## 合约结构

UniswapERC20--被继承>>UniswapPair--被引用>>UniswapFactory

UniswapPair继承了UniswapERC20合约，然后UniswapPair被工厂函数调用，层级分明；

## 创建流动性

项目方--创建流动性>>UniswapRouter--调用>>UniswapFactory--部署>>UniswapPair

项目方添加流动性会先通过路由合约，然后调用工厂合约去部署流动性对

## 交易

用户--交易>>UniswapV2Router--调用>>UniswapV2Pair

用户发起交易后会进入到路由合约，然后再进入到对应的交易对，执行pair合约



