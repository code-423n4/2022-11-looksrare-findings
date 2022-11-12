## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | Empty Blocks Should Be Removed Or Emit Something | 4 | - |
| [GAS&#x2011;2](#GAS&#x2011;2) | Use calldata instead of memory for function parameters | 1 | 300 |
| [GAS&#x2011;3](#GAS&#x2011;3) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 10 | - |
| [GAS&#x2011;4](#GAS&#x2011;4) | Optimize names to save gas | All in-scope contracts | 308 |
| [GAS&#x2011;5](#GAS&#x2011;5) | Use `uint256(1)`/`uint256(2)` instead for `true` and `false` boolean states | 2 | 10000 |
| [GAS&#x2011;6](#GAS&#x2011;6) | Duplicate imports | 4 | - |

Total: 355 contexts over 6 issues

## Gas Optimizations


### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. 

If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### <ins>Proof Of Concept</ins>

```
220: receive() external payable {}
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/LooksRareAggregator.sol#L220

```
132: } catch {}
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/proxies/LooksRareProxy.sol#L132

```
86: receive() external payable {}
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/proxies/SeaportProxy.sol#L86

```
217: } catch {}
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/proxies/SeaportProxy.sol#L217



### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Use calldata instead of memory for function parameters

In some cases, having function arguments in calldata instead of
memory is more optimal.

Consider the following generic example:
```
contract C {
	function add(uint[] memory arr) external returns (uint sum) {
		uint length = arr.length;
		for (uint i = 0; i < arr.length; i++) {
		    sum += arr[i];
		}
	}
}
```
In the above example, the dynamic array arr has the storage location
memory. When the function gets called externally, the array values are
kept in calldata and copied to memory during ABI decoding (using the
opcode calldataload and mstore). And during the for loop, arr[i]
accesses the value in memory using a mload. However, for the above
example this is inefficient. Consider the following snippet instead:
```
contract C {
	function add(uint[] calldata arr) external returns (uint sum) {
		uint length = arr.length;
		for (uint i = 0; i < arr.length; i++) {
		    sum += arr[i];
		}
	}
}
```
In the above snippet, instead of going via memory, the value is directly
read from calldata using calldataload. That is, there are no
intermediate memory operations that carries this value.

Gas savings: In the former example, the ABI decoding begins with
copying value from calldata to memory in a for loop. Each iteration
would cost at least 60 gas. In the latter example, this can be
completely avoided. This will also reduce the number of instructions and
therefore reduces the deploy time cost of the contract.

In short, use calldata instead of memory if the function argument
is only read.

Note that in older Solidity versions, changing some function arguments
from memory to calldata may cause "unimplemented feature error".
This can be avoided by using a newer (0.8.*) Solidity compiler.

Examples
Note: The following pattern is prevalent in the codebase:
```
function f(bytes memory data) external {
	(...) = abi.decode(data, (..., types, ...));
}
```
Here, changing to bytes calldata will decrease the gas. The total
savings for this change across all such uses would be quite
significant.

#### <ins>Proof Of Concept</ins>


```
function onERC1155BatchReceived(
        address,
        address,
        uint256[] memory,
        uint256[] memory,
        bytes memory
    ) external virtual returns (bytes4) {
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/TokenReceiver.sol#L24






### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>


```
188: uint120 numerator;
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/libraries/seaport/ConsiderationStructs.sol#L188

```
189: uint120 denominator;
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/libraries/seaport/ConsiderationStructs.sol#L189

```
31: uint120 numerator;
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/proxies/SeaportProxy.sol#L31

```
32: uint120 denominator;
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/proxies/SeaportProxy.sol#L32






### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Optimize names to save gas

`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://github.com/enzosv/solidity-optimize-name) link for an example of how it works. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

#### <ins>Proof Of Concept</ins>

Applies to all in-scope contracts.






### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Use `uint256(1)`/`uint256(2)` instead for `true` and `false` boolean states

If you don't use boolean for storage you will avoid Gwarmaccess 100 gas. In addition, state changes of boolean from `true` to `false` can cost up to ~20000 gas rather than `uint256(2)` to `uint256(1)` that would cost significantly less.

#### <ins>Proof Of Concept</ins>


```
45: mapping(address => mapping(bytes4 => bool)) private _proxyFunctionSelectors;
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/LooksRareAggregator.sol#L45

```
133: _proxyFunctionSelectors[proxy][selector] = true;
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/LooksRareAggregator.sol#L133




### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Duplicate imports

The contract `LookRareAggregator.sol` contains multiple duplicate imports.

#### <ins>Proof Of Concept</ins>
File: LooksRareAggregator.sol

```
9: import {LooksRareProxy} from "./proxies/LooksRareProxy.sol";
15: import {LooksRareProxy} from "./proxies/LooksRareProxy.sol";
```

```
11: import {TokenRescuer} from "./TokenRescuer.sol";
17: import {TokenRescuer} from "./TokenRescuer.sol";
```

```
12: import {TokenReceiver} from "./TokenReceiver.sol";
16: import {TokenReceiver} from "./TokenReceiver.sol";
```

```
10: import {BasicOrder, TokenTransfer} from "./libraries/OrderStructs.sol";
14: import {BasicOrder, FeeData, TokenTransfer} from "./libraries/OrderStructs.sol";
```

