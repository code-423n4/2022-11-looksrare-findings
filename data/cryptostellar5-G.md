### G-01 INTERNAL FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE REMOVED TO SAVE DEPLOYMENT GAS

*Number of Instances Identified: 4*

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol

```
125: function _setupDelayForRenouncingOwnership(uint256 _delay) internal
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol

```
72: function _verify(...) internal view
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol

```
23: function _executeERC1155SafeTransferFrom(...) internal
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol

```
54: function _returnETHIfAnyWithOneWeiLeft() internal
```


### G-02 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

*Number of Instances Identified: 5*

Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol

```
56: function _recoverEOASigner(bytes32 hash, bytes memory signature) internal pure returns (address signer)
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol

```
20: function _executeERC20Approve(...) internal
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol

```
24: function _executeERC20TransferFrom(...) internal
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol

```
21: function _executeERC721TransferFrom(...) internal
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol

```
45: function _executeERC1155SafeBatchTransferFrom(...) internal
```


### G-03 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Number of Instances Identified: 33*

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol

```
109: if (orders[i].currency == address(0)) ethValue += orders[i].price;
150: fee += orderFee;
209: fee += orderFee;
```

### G-04  USING BOOLS FOR STORAGE INCURS OVERHEAD

*Number of Instances Identified: 21*

```
    // Booleans are more expensive than uint256 or any type that takes up a full    // word because each write operation emits an extra SLOAD to first read the    // slot's contents, replace the bits taken up by the boolean, and then write    // back. This is the compiler's defense against contract upgrades and    // pointer aliasing, and it cannot be disabled.
```

[https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol

```
32: bool isAtomic
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol

```
45: mapping(address => mapping(bytes4 => bool)) private _proxyFunctionSelectors;
56: bool isAtomic
73:  (bytes memory proxyCalldata, bool maxFeeBpViolated) = _encodeCalldataAndValidateFeeBp(
88: (bool success, bytes memory returnData) = singleTradeData.proxy.delegatecall(proxyCalldata);
225: bool isAtomic
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20EnabledLooksRareAggregator.sol

```
19: bool isAtomic
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol

```
30: bool isAtomic
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IProxy.sol

```
26: bool isAtomic
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/seaport/ConsiderationStructs.sol

```
186: bool isValidated;
187: bool isCancelled;
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol

```
25: (bool status, bytes memory data) = currency.call(abi.encodeWithSelector(IERC20.approve.selector, to, amount));
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol

```
30: (bool status, bytes memory data) = currency.call
51: (bool status, bytes memory data) = currency.call(abi.encodeWithSelector(IERC20.transfer.selector, to, amount));
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol

```
27: (bool status, ) = collection.call(abi.encodeWithSelector(IERC721.transferFrom.selector, from, to, tokenId));
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol

```
30: (bool status, ) = collection.call(
52: (bool status, ) = collection.call(
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol

```
20: bool status;
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol

```
55: bool isAtomic
112: bool isAtomic
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol

```
65: bool isAtomic
```


### G-05 INLINE A MODIFIER THATS ONLY USED ONCE

*Number of Instances Identified: 1*

`nonReentrant()` modifier is used just once in [LooksRareAggregator.sol#L57](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L57) . This can be inlined to save gas

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ReentrancyGuard.sol

```
21: modifier nonReentrant()
```

### G-06 USAGE OF UINTS or INTS SMALLER THAN 32 BYTES - 256 BITS INCURS OVERHEAD

*Number of Instances Identified: 3*

> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol

```
25: uint8 v
57: (bytes32 r, bytes32 s, uint8 v) = _splitSignature(signature);
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol

```
84: (bytes32 r, bytes32 s, uint8 v) = _splitSignature(order.signature);
```
