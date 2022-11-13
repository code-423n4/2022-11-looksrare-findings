
##  Multiple `address` mappings can be combined into a single mapping of `address` to a struct, where appropriate.

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot

```
    mapping(address => mapping(bytes4 => bool)) private _proxyFunctionSelectors;
    mapping(address => FeeData) private _proxyFeeData;
```
>File: contracts/LooksRareAggregator.sol [(line 45-46)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L45-L46)



## `internal` functions only called once can be `inlined` to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.


```
    function _splitSignature(bytes memory signature)
        internal
```
>File: contracts/SignatureChecker.sol [(line 19-20)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L19-L20)

```
    function _recoverEOASigner(bytes32 hash, bytes memory signature) internal pure returns (address signer) {
```
>File: contracts/SignatureChecker.sol [(line 56)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L56)




##  `abi.encode()` is less efficient than `abi.encodePacked()`

```
        ExtraData memory extraDataStruct = abi.decode(extraData, (ExtraData));
```
>File: contracts/proxies/SeaportProxy.sol [(line 98)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L98)

```
            OrderExtraData memory orderExtraData = abi.decode(ordersExtraData[i], (OrderExtraData));
```
>File: contracts/proxies/SeaportProxy.sol [(line 103)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L103)

```
Other instances of this issue are :
```
* https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L188



## Usage `of uints/ints` smaller than `32 bytes (256 bits)` incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.


```
    uint120 numerator;
    uint120 denominator;
```
>File: contracts/libraries/seaport/ConsiderationStructs.sol [(line 172-L173)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/seaport/ConsiderationStructs.sol#L172-L173)

```
    uint120 numerator;
    uint120 denominator;
```
>File: contracts/libraries/seaport/ConsiderationStructs.sol [(line 188-L189)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/seaport/ConsiderationStructs.sol#L188-L189)
```
Other instances of this issue are :
```
* https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L31-L32
* https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L25
* https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L57




## call `emit` from `storage` is more expensive

Emmitting an event using storage data is more expensive

```
        emit NewOwner(owner);

```
>File: contracts/OwnableTwoSteps.sol [(line 91)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L91)
```
        emit InitiateOwnershipRenouncement(earliestOwnershipRenouncementTime);

```
>File: contracts/OwnableTwoSteps.sol [(line 116)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L116)
```
Other instances of this issue are :
```
* https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L104




## `internal` functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override
```
    function _setupDelayForRenouncingOwnership(uint256 _delay) internal {
```
>File: contracts/OwnableTwoSteps.sol [(line 125)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L125)

```
    function _verify(
        bytes32 hash,
        address signer,
        bytes memory signature
    ) internal view {
```
>File: contracts/SignatureChecker.sol [(line 72-76)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L72-L76)
```
Other instances of this issue are :
```
* https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L20-L24
* https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L24-L29
* https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L46-L50
* https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L21-L26
* https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L23-L29
* https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L45-L51
