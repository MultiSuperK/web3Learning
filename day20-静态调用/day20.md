# STATICCALL

### 只读式的调用，不允许在调用期间对状态进行任何修改

### EIP: https://eips.ethereum.org/EIPS/eip-214



可用于打包只读方法，节省gas

### 示例代码

```solidity
contract MultiCall{
	/*
	args:
		targets: 目标合约地址列表
		data: 目标合约接口的abi编码
	*/
	function multiCall(address[] targets, bytes[] calldata data) 
	external view returns (bytes[] memory){
		require(targets.length == data.length, 'value err!');
		bytes[] memory res_arr = new bytes[](data.length);
		
		for (uint i; i< targets.length; ++i){
			(bool success, bytes memory result) =
			targets[i].staticcall(data[i]);
			require(success, 'call failed');
			res_arr[i] = result
		}
		
		return res_arr
  }
}
```

