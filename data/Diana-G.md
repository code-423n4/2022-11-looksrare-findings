
## G-01 USING BOOLS FOR STORAGE INCURS OVERHEAD

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

_There are 31 instances of this issue_

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol

```
File: contracts/ERC20EnabledLooksRareAggregator.sol

32: bool isAtomic
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol

```
File: contracts/LooksRareAggregator.sol

56: bool isAtomic
73: (bytes memory proxyCalldata, bool maxFeeBpViolated) = _encodeCalldataAndValidateFeeBp(
88: (bool success, bytes memory returnData) = singleTradeData.proxy.delegatecall(proxyCalldata);
225: bool isAtomic
226: ) private view returns (bytes memory proxyCalldata, bool maxFeeBpViolated) {
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20EnabledLooksRareAggregator.sol

```
File: contracts/interfaces/IERC20EnabledLooksRareAggregator.sol

19: bool isAtomic
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC721.sol

```
File: contracts/interfaces/IERC721.sol

9: event ApprovalForAll(address indexed owner, address indexed operator, bool approved);
36: function setApprovalForAll(address operator, bool _approved) external;
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC1155.sol

```
File: contracts/interfaces/IERC1155.sol

15: event ApprovalForAll(address indexed account, address indexed operator, bool approved);
26: function setApprovalForAll(address operator, bool approved) external;
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol

```
File: contracts/interfaces/ILooksRareAggregator.sol

30: bool isAtomic
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IProxy.sol

```
File: contracts/interfaces/IProxy.sol

26: bool isAtomic,
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/SeaportInterface.sol

```
File: contracts/interfaces/SeaportInterface.sol

44: function fulfillBasicOrder(BasicOrderParameters calldata parameters) external payable returns (bool fulfilled);
68: function fulfillOrder(Order calldata order, bytes32 fulfillerConduitKey) external payable returns (bool fulfilled);
115: ) external payable returns (bool fulfilled);
323: function cancel(OrderComponents[] calldata orders) external returns (bool cancelled);
381: bool isValidated,
382: bool isCancelled,
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/seaport/ConsiderationStructs.sol

```
File: contracts/libraries/seaport/ConsiderationStructs.sol

186: bool isValidated;
187: bool isCancelled;
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol

```
File: contracts/lowLevelCallers/LowLevelERC20Approve.sol

25: (bool status, bytes memory data) = currency.call(abi.encodeWithSelector(IERC20.approve.selector, to, amount));
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol

```
File: contracts/lowLevelCallers/LowLevelERC20Transfer.sol

30: (bool status, bytes memory data) = currency.call(
51: (bool status, bytes memory data) = currency.call(abi.encodeWithSelector(IERC20.transfer.selector, to, amount));
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol

```
File: contracts/lowLevelCallers/LowLevelERC721Transfer.sol

27: (bool status, ) = collection.call(abi.encodeWithSelector(IERC721.transferFrom.selector, from, to, tokenId));
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol

```
File: contracts/lowLevelCallers/LowLevelERC1155Transfer.sol

30: (bool status, ) = collection.call(
52: (bool status, ) = collection.call(
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol

```
File: contracts/lowLevelCallers/LowLevelETH.sol

20: bool status;
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol

```
File: contracts/proxies/LooksRareProxy.sol

55: bool isAtomic,
112: bool isAtomic
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol

```
File: ccontracts/proxies/SeaportProxy.sol

65: bool isAtomic,
```

---------

## G-02 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

_There are 5 instances of this issue_

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol

```
File: contracts/SignatureChecker.sol

56: function _recoverEOASigner(bytes32 hash, bytes memory signature) internal pure returns (address signer) {
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol

```
File: contracts/lowLevelCallers/LowLevelERC20Approve.sol

20: function _executeERC20Approve(
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol

```
File: contracts/lowLevelCallers/LowLevelERC20Transfer.sol

24: function _executeERC20TransferFrom(
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol

```
File: contracts/lowLevelCallers/LowLevelERC721Transfer.sol

21: function _executeERC721TransferFrom(
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol

```
File: contracts/lowLevelCallers/LowLevelERC1155Transfer.sol

45: function _executeERC1155SafeBatchTransferFrom(
```

-----------

## G-03 INTERNAL FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE REMOVED TO SAVE DEPLOYMENT GAS 

_There are 4 instances of this issue_

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol

```
File: contracts/OwnableTwoSteps.sol

125: function _setupDelayForRenouncingOwnership(uint256 _delay) internal {
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol

```
File: contracts/SignatureChecker.sol

72: function _verify(
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol

```
File: contracts/lowLevelCallers/LowLevelERC1155Transfer.sol

23: function _executeERC1155SafeTransferFrom(
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol

```
File: contracts/lowLevelCallers/LowLevelETH.sol

54: function _returnETHIfAnyWithOneWeiLeft() internal {
```

----------

## G-04 x += y COSTS MORE GAS THAN x = x + y FOR STATE VARIABLES (SAME APPLIES FOR x -= y , x = x - y)

_There are 3 instances of this issue_

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol

```
File: ccontracts/proxies/SeaportProxy.sol

109: if (orders[i].currency == address(0)) ethValue += orders[i].price;
150: fee += orderFee;
209: fee += orderFee;
```

-----------

## G-05 USAGE OF UINTS or INTS SMALLER THAN 32 BYTES - 256 BITS INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

_There are 3 instances of this issue_

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol

```
File: contracts/SignatureChecker.sol

25: uint8 v
57: (bytes32 r, bytes32 s, uint8 v) = _splitSignature(signature);
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol

```
File: contracts/proxies/LooksRareProxy.sol

84: (bytes32 r, bytes32 s, uint8 v) = _splitSignature(order.signature);
```

------------

