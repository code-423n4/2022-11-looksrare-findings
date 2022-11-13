## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate | 1 | - |
| [G&#x2011;02] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 3 | 360 |
| [G&#x2011;03] | State variables should be cached in stack variables rather than re-reading them from storage | 2 | 194 |
| [G&#x2011;04] | Optimize names to save gas | 8 | 176 |
| [G&#x2011;05] | `internal` functions not called by the contract should be removed to save deployment gas | 2 | - |
| [G&#x2011;06] | Functions guaranteed to revert when called by normal users can be marked `payable` | 13 | 273 |

Total: 29 instances over 6 issues with **1003 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.



## Gas Optimizations

### [G&#x2011;01]  Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (**20000 gas**) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save **~42 gas per access** due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

*There is 1 instance of this issue:*
```solidity
File: contracts/LooksRareAggregator.sol

45        mapping(address => mapping(bytes4 => bool)) private _proxyFunctionSelectors;
46:       mapping(address => FeeData) private _proxyFeeData;

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L45-L46

```diff
diff --git a/contracts/LooksRareAggregator.sol b/contracts/LooksRareAggregator.sol
index f215f39..a5525aa 100644
--- a/contracts/LooksRareAggregator.sol
+++ b/contracts/LooksRareAggregator.sol
@@ -156,8 +156,10 @@ contract LooksRareAggregator is
         address recipient
     ) external onlyOwner {
         if (bp > 10000) revert FeeTooHigh();
-        _proxyFeeData[proxy].bp = bp;
-        _proxyFeeData[proxy].recipient = recipient;
+
+        FeeData storage pfd = _proxyFeeData[proxy];
+        pfd.bp = bp;
+        pfd.recipient = recipient;
 
         emit FeeUpdated(proxy, bp, recipient);
     }
```

Note that the numbers below are wrong due to [this](https://gist.github.com/0xA5DF/cc71e507d45fd51d708b3e8318654ce5) forge bug, where forge doesn't properly track warm/cold slots in tests
```diff
diff --git a/tmp/gas_before b/tmp/gas_after
index a3222f9..705e068 100644
--- a/tmp/gas_before
+++ b/tmp/gas_after
@@ -17 +17 @@
-│ 2504484                                                        ┆ 12343           ┆        ┆        ┆        ┆         │
+│ 2504891                                                        ┆ 12345           ┆        ┆        ┆        ┆         │
@@ -47 +47 @@
-│ setFee                                                         ┆ 2945            ┆ 43626  ┆ 47171  ┆ 49171  ┆ 21      │
+│ setFee                                                         ┆ 2945            ┆ 43631  ┆ 47176  ┆ 49176  ┆ 21      │
@@ -230 +230 @@
-│ 13089423                                                                    ┆ 64155           ┆     ┆        ┆     ┆         │
+│ 13089823                                                                    ┆ 64157           ┆     ┆        ┆     ┆         │
@@ -346,0 +347 @@
+Overall gas change: 4929 (0.099%)
```


### [G&#x2011;02]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 3 instances of this issue:*
```solidity
File: contracts/proxies/LooksRareProxy.sol

50        function execute(
51            BasicOrder[] calldata orders,
52            bytes[] calldata ordersExtraData,
53            bytes memory,
54            address recipient,
55            bool isAtomic,
56            uint256,
57:           address

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L50-L57

```solidity
File: contracts/TokenReceiver.sol

14        function onERC1155Received(
15            address,
16            address,
17            uint256,
18            uint256,
19            bytes memory
20:       ) external virtual returns (bytes4) {

24        function onERC1155BatchReceived(
25            address,
26            address,
27            uint256[] memory,
28            uint256[] memory,
29            bytes memory
30:       ) external virtual returns (bytes4) {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenReceiver.sol#L14-L20


```diff
diff --git a/contracts/TokenReceiver.sol b/contracts/TokenReceiver.sol
index a82e815..759d4ac 100644
--- a/contracts/TokenReceiver.sol
+++ b/contracts/TokenReceiver.sol
@@ -16,7 +16,7 @@ abstract contract TokenReceiver {
         address,
         uint256,
         uint256,
-        bytes memory
+        bytes calldata
     ) external virtual returns (bytes4) {
         return this.onERC1155Received.selector;
     }
@@ -24,9 +24,9 @@ abstract contract TokenReceiver {
     function onERC1155BatchReceived(
         address,
         address,
-        uint256[] memory,
-        uint256[] memory,
-        bytes memory
+        uint256[] calldata,
+        uint256[] calldata,
+        bytes calldata
     ) external virtual returns (bytes4) {
         return this.onERC1155BatchReceived.selector;
     }
diff --git a/contracts/proxies/LooksRareProxy.sol b/contracts/proxies/LooksRareProxy.sol
index cbce118..1ebe936 100644
--- a/contracts/proxies/LooksRareProxy.sol
+++ b/contracts/proxies/LooksRareProxy.sol
@@ -50,7 +50,7 @@ contract LooksRareProxy is IProxy, TokenRescuer, TokenTransferrer, SignatureChec
     function execute(
         BasicOrder[] calldata orders,
         bytes[] calldata ordersExtraData,
-        bytes memory,
+        bytes calldata,
         address recipient,
         bool isAtomic,
         uint256,
```

```diff
diff --git a/tmp/gas_before b/tmp/gas_after
index a3222f9..94f3986 100644
--- a/tmp/gas_before
+++ b/tmp/gas_after
@@ -17 +17 @@
-│ 2504484                                                        ┆ 12343           ┆        ┆        ┆        ┆         │
+│ 2420786                                                        ┆ 11925           ┆        ┆        ┆        ┆         │
@@ -27 +27 @@
-│ execute                                                        ┆ 1370            ┆ 434636 ┆ 429633 ┆ 960082 ┆ 67      │
+│ execute                                                        ┆ 1370            ┆ 434530 ┆ 429633 ┆ 960082 ┆ 67      │
@@ -31 +31 @@
-│ onERC1155Received                                              ┆ 1631            ┆ 1631   ┆ 1631   ┆ 1631   ┆ 4       │
+│ onERC1155Received                                              ┆ 1305            ┆ 1305   ┆ 1305   ┆ 1305   ┆ 4       │
@@ -56 +56 @@
-│ 1402373                                                    ┆ 6944            ┆        ┆        ┆        ┆         │
+│ 1321684                                                    ┆ 6541            ┆        ┆        ┆        ┆         │
@@ -62 +62 @@
-│ execute                                                    ┆ 268230          ┆ 373671 ┆ 361477 ┆ 503500 ┆ 4       │
+│ execute                                                    ┆ 268230          ┆ 373543 ┆ 361349 ┆ 503244 ┆ 4       │
@@ -71 +71 @@
-│ 1683078                                                      ┆ 8635            ┆        ┆        ┆        ┆         │
+│ 1699292                                                      ┆ 8716            ┆        ┆        ┆        ┆         │
@@ -75 +75 @@
-│ execute                                                      ┆ 1962            ┆ 260929 ┆ 279156 ┆ 506876 ┆ 23      │
+│ execute                                                      ┆ 1640            ┆ 260616 ┆ 278900 ┆ 506620 ┆ 23      │
@@ -230 +230 @@
-│ 13089423                                                                    ┆ 64155           ┆     ┆        ┆     ┆         │
+│ 12924886                                                                    ┆ 63334           ┆     ┆        ┆     ┆         │
@@ -288 +288 @@
-│ mint                                                    ┆ 26410           ┆ 28497 ┆ 26410  ┆ 31628 ┆ 5       │
+│ mint                                                    ┆ 26410           ┆ 28366 ┆ 26410  ┆ 31302 ┆ 5       │
@@ -346,0 +347 @@
+Overall gas change: -1174338 (-24.848%)
```



### [G&#x2011;03]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 2 instances of this issue:*
```solidity
File: contracts/OwnableTwoSteps.sol

/// @audit owner on line 87
91:           emit NewOwner(owner);

/// @audit earliestOwnershipRenouncementTime on line 114
116:          emit InitiateOwnershipRenouncement(earliestOwnershipRenouncementTime);

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L91


```diff
diff --git a/contracts/OwnableTwoSteps.sol b/contracts/OwnableTwoSteps.sol
index 99d8a7d..366bd03 100644
--- a/contracts/OwnableTwoSteps.sol
+++ b/contracts/OwnableTwoSteps.sol
@@ -84,11 +84,10 @@ abstract contract OwnableTwoSteps is IOwnableTwoSteps {
         if (ownershipStatus != Status.TransferInProgress) revert TransferNotInProgress();
         if (msg.sender != potentialOwner) revert WrongPotentialOwner();
 
-        owner = msg.sender;
         delete ownershipStatus;
         delete potentialOwner;
 
-        emit NewOwner(owner);
+        emit NewOwner(owner = msg.sender);
     }
 
     /**
@@ -111,9 +110,8 @@ abstract contract OwnableTwoSteps is IOwnableTwoSteps {
         if (ownershipStatus != Status.NoOngoingTransfer) revert TransferAlreadyInProgress();
 
         ownershipStatus = Status.RenouncementInProgress;
-        earliestOwnershipRenouncementTime = block.timestamp + delay;
 
-        emit InitiateOwnershipRenouncement(earliestOwnershipRenouncementTime);
+        emit InitiateOwnershipRenouncement(earliestOwnershipRenouncementTime = block.timestamp + delay);
     }
 
     /**
```

Note that the numbers below are wrong due to [this](https://gist.github.com/0xA5DF/cc71e507d45fd51d708b3e8318654ce5) forge bug, where forge doesn't properly track warm/cold slots in tests
```diff
diff --git a/tmp/gas_before b/tmp/gas_after
index a3222f9..f393f3c 100644
--- a/tmp/gas_before
+++ b/tmp/gas_after
@@ -174 +174 @@
-│ confirmOwnershipTransfer                                               ┆ 507             ┆ 2329  ┆ 2379   ┆ 4101  ┆ 3       │
+│ confirmOwnershipTransfer                                               ┆ 507             ┆ 2328  ┆ 2379   ┆ 4099  ┆ 3       │
@@ -180 +180 @@
-│ initiateOwnershipRenouncement                                          ┆ 529             ┆ 27941 ┆ 46039  ┆ 50039 ┆ 7       │
+│ initiateOwnershipRenouncement                                          ┆ 529             ┆ 27942 ┆ 46042  ┆ 50042 ┆ 7       │
@@ -346,0 +347 @@
+Overall gas change: 7 (0.008%)
```



### [G&#x2011;04]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 8 instances of this issue:*
```solidity
File: contracts/ERC20EnabledLooksRareAggregator.sol

/// @audit execute()
15:   contract ERC20EnabledLooksRareAggregator is IERC20EnabledLooksRareAggregator, LowLevelERC20Transfer {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/ERC20EnabledLooksRareAggregator.sol#L15

```solidity
File: contracts/interfaces/IERC20EnabledLooksRareAggregator.sol

/// @audit execute()
7:    interface IERC20EnabledLooksRareAggregator {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC20EnabledLooksRareAggregator.sol#L7

```solidity
File: contracts/interfaces/ILooksRareAggregator.sol

/// @audit execute()
6:    interface ILooksRareAggregator {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/ILooksRareAggregator.sol#L6

```solidity
File: contracts/interfaces/IProxy.sol

/// @audit execute()
6:    interface IProxy {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IProxy.sol#L6

```solidity
File: contracts/interfaces/SeaportInterface.sol

/// @audit fulfillBasicOrder(), fulfillOrder(), fulfillAdvancedOrder(), fulfillAvailableOrders(), fulfillAvailableAdvancedOrders(), matchOrders(), matchAdvancedOrders(), cancel(), validate(), incrementCounter(), getOrderHash(), getOrderStatus(), getCounter(), information()
28:   interface SeaportInterface {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/SeaportInterface.sol#L28

```solidity
File: contracts/LooksRareAggregator.sol

/// @audit execute(), setERC20EnabledLooksRareAggregator(), addFunction(), removeFunction(), setFee(), approve(), supportsProxyFunction(), rescueERC721(), rescueERC1155()
25:   contract LooksRareAggregator is

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L25

```solidity
File: contracts/OwnableTwoSteps.sol

/// @audit cancelOwnershipTransfer(), confirmOwnershipRenouncement(), confirmOwnershipTransfer(), initiateOwnershipTransfer(), initiateOwnershipRenouncement()
11:   abstract contract OwnableTwoSteps is IOwnableTwoSteps {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L11

```solidity
File: contracts/TokenRescuer.sol

/// @audit rescueETH(), rescueERC20()
14:   contract TokenRescuer is OwnableTwoSteps, LowLevelETH, LowLevelERC20Transfer {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenRescuer.sol#L14

### [G&#x2011;05]  `internal` functions not called by the contract should be removed to save deployment gas
If the functions are required by an interface, the contract should inherit from that interface and use the `override` keyword

*There are 2 instances of this issue:*
```solidity
File: contracts/lowLevelCallers/LowLevelERC1155Transfer.sol

23        function _executeERC1155SafeTransferFrom(
24            address collection,
25            address from,
26            address to,
27            uint256 tokenId,
28:           uint256 amount

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L23-L28

```solidity
File: contracts/lowLevelCallers/LowLevelETH.sol

54        function _returnETHIfAnyWithOneWeiLeft() internal {
55:           assembly {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelETH.sol#L54-L55

### [G&#x2011;06]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 13 instances of this issue:*
```solidity
File: contracts/LooksRareAggregator.sol

120:      function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external onlyOwner {

132:      function addFunction(address proxy, bytes4 selector) external onlyOwner {

143:      function removeFunction(address proxy, bytes4 selector) external onlyOwner {

153       function setFee(
154           address proxy,
155           uint256 bp,
156           address recipient
157:      ) external onlyOwner {

173       function approve(
174           address marketplace,
175           address currency,
176           uint256 amount
177:      ) external onlyOwner {

197       function rescueERC721(
198           address collection,
199           address to,
200           uint256 tokenId
201:      ) external onlyOwner {

213       function rescueERC1155(
214           address collection,
215           address to,
216           uint256[] calldata tokenIds,
217           uint256[] calldata amounts
218:      ) external onlyOwner {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L120

```solidity
File: contracts/OwnableTwoSteps.sol

51:       function cancelOwnershipTransfer() external onlyOwner {

68:       function confirmOwnershipRenouncement() external onlyOwner {

98:       function initiateOwnershipTransfer(address newPotentialOwner) external onlyOwner {

110:      function initiateOwnershipRenouncement() external onlyOwner {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L51

```solidity
File: contracts/TokenRescuer.sol

22:       function rescueETH(address to) external onlyOwner {

34:       function rescueERC20(address currency, address to) external onlyOwner {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenRescuer.sol#L22


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | `<array>.length` should not be looked up in every loop of a `for`-loop | 2 | 6 |
| [G&#x2011;02] | Using `bool`s for storage incurs overhead | 1 | 17100 |

Total: 3 instances over 2 issues with **17106 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.



## Gas Optimizations

### [G&#x2011;01]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There are 2 instances of this issue:*
```solidity
File: contracts/proxies/SeaportProxy.sol

/// @audit (valid but excluded finding)
126:          for (uint256 i; i < availableOrders.length; ) {

/// @audit (valid but excluded finding)
187:          for (uint256 i; i < orders.length; ) {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L126

### [G&#x2011;02]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There is 1 instance of this issue:*
```solidity
File: contracts/LooksRareAggregator.sol

/// @audit (valid but excluded finding)
45:       mapping(address => mapping(bytes4 => bool)) private _proxyFunctionSelectors;

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L45
