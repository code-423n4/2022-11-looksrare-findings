# LooksRare Aggregator Contest Gas Optimization Report

## Summary

The following gas optimization issues were found during the code audit:

1. Use `calldata` instead of `memory` (3 instances)
2. Cache `<array>.length` (2 instances)
3. Using `bool`s for storage incurs overhead (6 instances)
4. Use `!= 0` instead of `> 0` when comparing uint (7 instances)
5. Empty blocks should be removed or emit something (4 instances)
6. Functions guaranteed to revert when called by normal users can be marked `payable` (9 instances)

Total 31 instances of 6 issues.

## 1. Use `calldata` instead of `memory` (3 instances)

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for loop to copy each index of the `calldata` to the `memory` index. This overhead can be optimized by using `calldata` directly.

```solidity
contracts/SignatureChecker.sol::19 => function _splitSignature(bytes memory signature)

contracts/SignatureChecker.sol::56 => function _recoverEOASigner(bytes32 hash, bytes memory signature) internal pure returns (address signer) {

contracts/proxies/SeaportProxy.sol::227 => function _populateParameters(BasicOrder calldata order, OrderExtraData memory orderExtraData)
```

## 2. Cache `<array>.length` (2 instances)

If `<array>.length` is used as for loop termination condition, then the `.length` method will be called in each iteration. Caching it in a local variable can save gas.

```solidity
contracts/proxies/SeaportProxy.sol::126 => for (uint256 i; i < availableOrders.length; ) {

contracts/proxies/SeaportProxy.sol::187 => for (uint256 i; i < orders.length; ) {
```

## 3. Using `bool`s for storage incurs overhead (6 instances)

Use `uint256(1)` and `uint256(2)` for true/false. Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
contracts/ERC20EnabledLooksRareAggregator.sol::32 => bool isAtomic

contracts/LooksRareAggregator.sol::56 => bool isAtomic

contracts/LooksRareAggregator.sol::225 => bool isAtomic

contracts/proxies/LooksRareProxy.sol::55 => bool isAtomic,

contracts/proxies/LooksRareProxy.sol::112 => bool isAtomic

contracts/proxies/SeaportProxy.sol::65 => bool isAtomic,
```

## 4. Use `!= 0` instead of `> 0` when comparing uint (7 instances)

When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

```solidity
contracts/LooksRareAggregator.sol::92 => if (returnData.length > 0) {

contracts/LooksRareAggregator.sol::108 => if (tokenTransfersLength > 0) _returnERC20TokensIfAny(tokenTransfers, originator);

contracts/LooksRareAggregator.sol::245 => if (balance > 0) _executeERC20DirectTransfer(tokenTransfers[i].currency, recipient, balance);

contracts/proxies/SeaportProxy.sol::152 => if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);

contracts/proxies/SeaportProxy.sol::163 => if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);

contracts/proxies/SeaportProxy.sol::211 => if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);

contracts/proxies/SeaportProxy.sol::224 => if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
```

## 5. Empty blocks should be removed or emit something (4 instances)

Empty blocks exist in the code. The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

```solidity
contracts/LooksRareAggregator.sol::220 => receive() external payable {}

contracts/proxies/LooksRareProxy.sol::132 => } catch {}

contracts/proxies/SeaportProxy.sol::86 => receive() external payable {}

contracts/proxies/SeaportProxy.sol::217 => } catch {}
```

## 6. Functions guaranteed to revert when called by normal users can be marked `payable` (9 instances)

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE`(2), `DUP1`(3), `ISZERO`(3), `PUSH2`(3), `JUMPI`(10), `PUSH1`(3), `DUP1`(3), `REVERT`(0), `JUMPDEST`(1), `POP`(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

```solidity
contracts/LooksRareAggregator.sol::120 => function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external onlyOwner {

contracts/LooksRareAggregator.sol::132 => function addFunction(address proxy, bytes4 selector) external onlyOwner {

contracts/LooksRareAggregator.sol::143 => function removeFunction(address proxy, bytes4 selector) external onlyOwner {

contracts/OwnableTwoSteps.sol::51 => function cancelOwnershipTransfer() external onlyOwner {

contracts/OwnableTwoSteps.sol::68 => function confirmOwnershipRenouncement() external onlyOwner {

contracts/OwnableTwoSteps.sol::98 => function initiateOwnershipTransfer(address newPotentialOwner) external onlyOwner {

contracts/OwnableTwoSteps.sol::110 => function initiateOwnershipRenouncement() external onlyOwner {

contracts/TokenRescuer.sol::22 => function rescueETH(address to) external onlyOwner {

contracts/TokenRescuer.sol::34 => function rescueERC20(address currency, address to) external onlyOwner {
```
