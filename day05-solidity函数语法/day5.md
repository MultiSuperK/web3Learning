day5-solidity

### 常量

address public **constant** MY_ADDRESS



### 结构命令控制

if-else

```solidity
contract IfElse{
	function example(uint x) external pure returns (uint){
		if (x<20){
			return 1;
		}else if (x=20){
			return 2;
		}else{
			return 3;
		}
	}
	
	function ternaty(uint y) external pure returns (uint){
		return y > 20 ? 1 : 2
	}
}
```



### 循环控制

```solidity
pragma solidity ^0.8.11
contract IfElse{
	function loops() external pure {
		for (uint i=0; i<10; i++){
			if (i==3){
				continue
			}
			if (i==8){
				break;
			}
		}
	}
	
	function while_loops() external pure{
		uint i = 0;
		while (i < 10){
			emit i;
			i++;
		}
	}
	
	function sum(uint n) external pure returns (uint){
		uint s;
		
		assert n>10;
		
		for (uint i=1; i<=n; i++){
			s += i;
		}
		
		return s;
	}
}
```



### 异常

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

contract MyError{

    // require
    // gas 花费 21989
    uint public test_storage = 0;

	function testRequire(uint i) public returns (uint){
        test_storage = 0;
		require(i <= 10, "i should <= 10!!!");  // cost 24162
        test_storage += i;
        return test_storage;  //cost 44489
	}
	
    function testRevert(uint i) public returns (uint){
        test_storage = 0;

        if (i <= 10){  // 这里使用小于，会比使用小于等于少3个gas/
            revert("i should <= 10!!!"); // cost 24141  24138
        }
        test_storage += i;
        return test_storage;  //cost 44489
	}
	
	uint public GlobalNum = 9526;
	function testAssert(uint _i) public returns (uint){
		assert(GlobalNum == 9526);  // 23786

        GlobalNum += _i;
        return GlobalNum;  // 27273  limit=31364
	}
	
    // 限制变化值小于等于10
	function testModifyGlobalNum(uint i) public {
        require(i <= 10, "i <= 10");  // cost 21923
		GlobalNum += i; // 26857
	}
	
	//自定义异常
	error MyError(uint sender_pay);

	function testCustomError(uint i) public view returns (string memory){
		if (i > 10){
			revert MyError({sender_pay: i});
		} else if (i == 0){
            revert("i != 0");
        } else {
            return "good test!";
        }
	}
}
```





### 函数修改器（装饰器）

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

contract TestModifier{

    uint public count = 0;

    modifier token_checker(uint16 _token){
        require(_token == 9527, "token 404");
        _;
        require(count < 9527; "count < 9527!")
    }

    function add_count(uint16 token) external token_checker(token) returns (uint){
        count += 1;
        return count;
    }

    function sub_count(uint16 token) external token_checker(token) returns (uint){
        count -= 1;
        return count;
    }
}
```



### 合约构造函数(本合约以奇怪的方式运行)

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.11;

contract TestModifier{

    address public programer;
    uint public total_supply;
    uint public res_count;
    uint public curr_count;

    struct StateToken{
        address owner;
        bytes32 code;
    }
    StateToken[] public state_tokens; 

    constructor(uint nft_count){
        programer = msg.sender;
        total_supply = nft_count;
        res_count = total_supply;
        curr_count = 0;
        // airdorp()
    }

    modifier token_checker(uint _token){
        require(_token == 9527, "token 404");
        _;
    }

    function mint(uint token) external token_checker(token) returns (StateToken memory){

        bytes32 _nft_code;
        if (res_count == 0){
            _nft_code = keccak256("1");
        } else if (res_count == 1){
            _nft_code = keccak256("2");
        } else {  // (res_count == 2)
            _nft_code = keccak256("3");
        }
        
        StateToken memory _nft = StateToken(msg.sender, _nft_code);
        state_tokens.push(_nft);
        curr_count += 1;
        res_count -= 1;
        return _nft;
    }
}
```