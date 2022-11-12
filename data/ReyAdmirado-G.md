
# Gas


| | issue |
| ----------- | ----------- |
| 1 | [can make the variable outside the loop to save gas](#1-can-make-the-variable-outside-the-loop-to-save-gas) |
| 2 | [internal or private functions only called once can be inlined to save gas](#2-internal-or-private-functions-only-called-once-can-be-inlined-to-save-gas) |
| 3 | [usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#3-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead) |
| 4 | [Move if-else to inside function call to save deployment gas](#4-move-if-else-to-inside-function-call-to-save-deployment-gas) |

## 1. can make the variable outside the loop to save gas

make the variable outside of loop and only assign it inside the loop

- [LooksRareProxy.sol#L84](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L84)

- [SeaportProxy.sol#L146](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L146)
- [SeaportProxy.sol#L147](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L147)
- [SeaportProxy.sol#L195](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L195)
- [SeaportProxy.sol#L196](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L196)
- [SeaportProxy.sol#L257](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L257)

- [LooksRareAggregator.sol#L244](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L244)


## 2. internal or private functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

`_executeSingleOrder`
- [LooksRareProxy.sol#L107](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L107)

`_executeAtomicOrders`
- [SeaportProxy.sol#L88](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L88)

`_handleFees`
- [SeaportProxy.sol#L136](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L136)

`_executeNonAtomicOrders`
- [SeaportProxy.sol#L178](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L178)

`_pullERC20Tokens`
- [ERC20EnabledLooksRareAggregator.sol#L39](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L39)

`_encodeCalldataAndValidateFeeBp`
- [LooksRareAggregator.sol#L222](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L222)

`_returnERC20TokensIfAny`
- [LooksRareAggregator.sol#L241](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L241)


## 3. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

- [LooksRareProxy.sol#L84](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L84)

- [SignatureChecker.sol#L25](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L25)
- [SignatureChecker.sol#L57](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L57)


## 4. Move if-else to inside function call to save deployment gas

- [SeaportProxy.sol#L74](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L74)
- [SeaportProxy.sol#L171](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L171)
