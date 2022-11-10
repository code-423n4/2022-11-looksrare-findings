- [Gas](#gas)
    - [**1. There's no need to set default values for variables**](#1-theres-no-need-to-set-default-values-for-variables)
        - [Total gas saved: **8 * 1 = 8**](#total-gas-saved-8--1--8)
    - [**2. Reorder structure layout**](#2-reorder-structure-layout)
    - [**3. Use calldata instead of memory**](#3-use-calldata-instead-of-memory)
    - [**4. Avoid input decoding**](#4-avoid-input-decoding)
    - [**5. Reuse keys**](#5-reuse-keys)

# Gas

## **1. There's no need to set default values for variables**

If a variable is not set/initialized, the default value is assumed (0, `false`, 0x0 ... depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testInit() public view returns (uint) { uint256 a = 0; return a; }
}

contract TesterB {
function testNoInit() public view returns (uint) { uint256 a; return a; }
}
```

Gas saving executing: **8 per entry**

```
TesterA.testInit:   21392
TesterB.testNoInit: 21384
```

**Affected source code:**

- [LooksRareProxy.sol:92](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/LooksRareProxy.sol#L92)

### Total gas saved: **8 * 1 = 8**

## **2. Reorder structure layout**

The following structures could be optimized moving the position of certain values in order to save a lot slots:

- `OrderComponents` in [ConsiderationStructs.sol:22-34](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/libraries/seaport/ConsiderationStructs.sol#L22-L34):

```diff
struct OrderComponents {
    address offerer;
    address zone;
+   OrderType orderType;
    OfferItem[] offer;
    ConsiderationItem[] consideration;
-   OrderType orderType;
    uint256 startTime;
    uint256 endTime;
    bytes32 zoneHash;
    uint256 salt;
    bytes32 conduitKey;
    uint256 counter;
}
```

- `BasicOrderParameters` in [ConsiderationStructs.sol:100-121](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/libraries/seaport/ConsiderationStructs.sol#L100-L121)

```diff
struct BasicOrderParameters {
    // calldata offset
    address considerationToken; // 0x24
    uint256 considerationIdentifier; // 0x44
    uint256 considerationAmount; // 0x64
    address payable offerer; // 0x84
    address zone; // 0xa4
    address offerToken; // 0xc4
+   BasicOrderType basicOrderType; // 0x124
    uint256 offerIdentifier; // 0xe4
    uint256 offerAmount; // 0x104
-   BasicOrderType basicOrderType; // 0x124
    uint256 startTime; // 0x144
    uint256 endTime; // 0x164
    bytes32 zoneHash; // 0x184
    uint256 salt; // 0x1a4
    bytes32 offererConduitKey; // 0x1c4
    bytes32 fulfillerConduitKey; // 0x1e4
    uint256 totalOriginalAdditionalRecipients; // 0x204
    AdditionalRecipient[] additionalRecipients; // 0x224
    bytes signature; // 0x244
    // Total length, excluding dynamic array data: 0x264 (580)
}
```

`OrderParameters` in [ConsiderationStructs.sol:139-152](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/libraries/seaport/ConsiderationStructs.sol#L139-L152)

```diff
struct OrderParameters {
    address offerer; // 0x00
    address zone; // 0x20
+   OrderType orderType; // 0x80
    OfferItem[] offer; // 0x40
    ConsiderationItem[] consideration; // 0x60
-   OrderType orderType; // 0x80
    uint256 startTime; // 0xa0
    uint256 endTime; // 0xc0
    bytes32 zoneHash; // 0xe0
    uint256 salt; // 0x100
    bytes32 conduitKey; // 0x120
    uint256 totalOriginalConsiderationItems; // 0x140
    // offer.length                          // 0x160
}
```

**Reference:**

- https://ethdebug.github.io/solidity-data-representation

> Enums are represented by integers; the possibility listed first by 0, the next by 1, and so forth. An enum type just acts like uintN, where N is the smallest legal value large enough to accomodate all the possibilities.

- https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html#storage-inplace-encoding

## **3. Use `calldata` instead of `memory`**

Some methods are declared as `external` but the arguments are defined as `memory` instead of as `calldata`.

By marking the function as `external` it is possible to use `calldata` in the arguments shown below and save significant gas.

**Recommended changes:**

- For [TokenReceiver.sol:4-34](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/TokenReceiver.sol#L4-L34):

```diff
abstract contract TokenReceiver {
    function onERC721Received(
        address,
        address,
        uint256,
        bytes calldata
    ) external pure returns (bytes4) {
        return this.onERC721Received.selector;
    }

    function onERC1155Received(
        address,
        address,
        uint256,
        uint256,
-       bytes memory
+       bytes calldata
    ) external virtual returns (bytes4) {
        return this.onERC1155Received.selector;
    }

    function onERC1155BatchReceived(
        address,
        address,
-       uint256[] memory,
-       uint256[] memory,
-       bytes memory
+       uint256[] calldata,
+       uint256[] calldata,
+       bytes calldata
    ) external virtual returns (bytes4) {
        return this.onERC1155BatchReceived.selector;
    }
}
```

- For [LooksRareProxy.sol:53](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/LooksRareProxy.sol#L53):

```diff
    function execute(
        BasicOrder[] calldata orders,
        bytes[] calldata ordersExtraData,
-       bytes memory,
+       bytes calldata,
        address recipient,
        bool isAtomic,
        uint256,
        address
    ) external payable override {
```

## **4. Avoid input decoding**

It's possible to avoid decoding logic from the `ordersExtraData` in `LooksRareProxy.execute`.

```diff
    function execute(
        BasicOrder[] calldata orders,
-       bytes[] calldata ordersExtraData,
+       OrderExtraData[] calldata ordersExtraData,
        bytes memory,
        address recipient,
        bool isAtomic,
        uint256,
        address
    ) external payable override {
        if (address(this) != aggregator) revert InvalidCaller();

        uint256 ordersLength = orders.length;
        if (ordersLength == 0 || ordersLength != ordersExtraData.length) revert InvalidOrderLength();

        for (uint256 i; i < ordersLength; ) {
            BasicOrder memory order = orders[i];

-           OrderExtraData memory orderExtraData = abi.decode(ordersExtraData[i], (OrderExtraData));
+           OrderExtraData memory orderExtraData = ordersExtraData[i];
```

**Affected source code:**

- [LooksRareProxy.sol:50-68](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/LooksRareProxy.sol#L50-L68)
- [SeaportProxy.sol:98](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/SeaportProxy.sol#L98)
- [SeaportProxy.sol:103](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/SeaportProxy.sol#L103)

## **5. Reuse keys**

It's possible to optimize the storage use of `LooksRareAggregator` joining the two mapping related to the proxy use to use only one with an struct.

```js
    mapping(address => mapping(bytes4 => bool)) private _proxyFunctionSelectors;
    mapping(address => FeeData) private _proxyFeeData;
```

Could be changed to:

```js
    struct Proxy {
        mapping(bytes4 => bool) functionSelectors;
        FeeData feeData;
    }

    mapping(address => Proxy) private _proxy;
```

**Affected source code:**

- [LooksRareAggregator.sol:45-46](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/LooksRareAggregator.sol#L45-L46)
