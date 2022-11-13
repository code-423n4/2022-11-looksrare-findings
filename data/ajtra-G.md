# Index
1. I = I + (-) X is cheaper in gas cost than I += (-=) X
2. Using storage instead of memory for structs/arrays
3. Cache address(aggregator) in _pullERC20Tokens to avoid accessing continuously to the storage
4. Functions guaranteed to revert when called by normal users can be marked payable	

# Details
## 1. I = I + (-) X is cheaper in gas cost than I += (-=) X
### Description
In the following example (optimizer = 10000) it's possible to demostrate that I = I + X cost less gas than I += X in state variable.

```solidity
contract Test_Optimization {
    uint256 a = 1;
    function Add () external returns (uint256) {
        a = a + 1;
        return a;
    }
}

contract Test_Without_Optimization {
    uint256 a = 1;
    function Add () external returns (uint256) {
        a += 1;
        return a;
    }
}
```
* Test_Optimization cost is 26324 gas
* Test_Without_Optimization cost is 26440 gas

With this optimization it's possible to **save 116 gas**

### Lines in the code
[SeaportProxy.sol#L109](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L109)
[SeaportProxy.sol#L150](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L150)
[SeaportProxy.sol#L209](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L209)
	
## 2. Using storage instead of memory for structs/arrays
### Description
When retrieving data from a memory location, assigning the data to a memory variable causes all fields of the struct/array to be read from memory, resulting in a Gcoldsload (2100 gas) for each field of the struct/array. When reading fields from new memory variables, they cause an extra MLOAD instead of a cheap stack read. Rather than declaring a variable with the memory keyword, it is much cheaper to declare a variable with the storage keyword and cache all fields that need to be read again in a stack variable, because the fields actually read will only result in a Gcoldsload. The only case where the entire struct/array is read into a memory variable is when the entire struct/array is returned by a function, passed to a function that needs memory, or when the array/struct is read from another store array/struc

### Lines in the code
[LooksRareProxy.sol#L65](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L65)
[LooksRareAggregator.sol#L227](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L227)
	
## 3. Cache address(aggregator) in _pullERC20Tokens to avoid accessing continuously to the storage
Cache address(aggregator) in a local variable to avoid accessing in every loop to the storage.

```diff
    function _pullERC20Tokens(TokenTransfer[] calldata tokenTransfers, address source) private {
        uint256 tokenTransfersLength = tokenTransfers.length;
+		address aggregadorAdr = address(aggregator);
        for (uint256 i; i < tokenTransfersLength; ) {
            _executeERC20TransferFrom(
                tokenTransfers[i].currency,
                source,
-               address(aggregator),
+				aggregadorAdr
                tokenTransfers[i].amount
            );
            unchecked {
                ++i;
            }
        }
    }
```	
[ERC20EnabledLooksRareAggregator.sol#L39-L52](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/ERC20EnabledLooksRareAggregator.sol#L39-L52)
	
## 4. Functions guaranteed to revert when called by normal users can be marked payable	
### Description
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. 
Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. 
The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), 
which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

### Lines in the code	
[OwnableTwoSteps.sol#L51](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L51)
[OwnableTwoSteps.sol#L68](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L68)
[OwnableTwoSteps.sol#L98](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L98)
[OwnableTwoSteps.sol#L110](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L110)
[TokenRescuer.sol#L22](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenRescuer.sol#L22)
[TokenRescuer.sol#L34](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenRescuer.sol#L34)
