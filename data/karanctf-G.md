## cache `.length` in loops
**Summary:**  Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the array length in the stack saves around 3 gas per iteration.

```solidity

proxies/SeaportProxy.sol:126:        for (uint256 i; i < availableOrders.length; ) {
proxies/SeaportProxy.sol:187:        for (uint256 i; i < orders.length; ) {
```
## Use `!= 0` instead of `> 0`
```solidity
lowLevelCallers/LowLevelERC20Approve.sol:28:        if (data.length > 0) {
lowLevelCallers/LowLevelERC20Transfer.sol:35:        if (data.length > 0) {
lowLevelCallers/LowLevelERC20Transfer.sol:54:        if (data.length > 0) {
proxies/SeaportProxy.sol:152:                if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
proxies/SeaportProxy.sol:163:        if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
proxies/SeaportProxy.sol:211:                        if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
proxies/SeaportProxy.sol:224:        if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
LooksRareAggregator.sol:92:                    if (returnData.length > 0) {
LooksRareAggregator.sol:108:        if (tokenTransfersLength > 0) _returnERC20TokensIfAny(tokenTransfers, originator);
LooksRareAggregator.sol:245:            if (balance > 0) _executeERC20DirectTransfer(tokenTransfers[i].currency, recipient, balance);
SignatureChecker.sol:46:        if (uint256(s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0) revert BadSignatureS();
```

## `<x> += <y>` costs more gas than `<x> = <x> + <y>` 
```solidity
proxies/SeaportProxy.sol:109:            if (orders[i].currency == address(0)) ethValue += orders[i].price;
proxies/SeaportProxy.sol:150:                fee += orderFee;
proxies/SeaportProxy.sol:209:                        fee += orderFee;
```


## [G-18] Use `calldata` instead of `memory` when used only for reading
**forat virable declare time only
```solidity

proxies/SeaportProxy.sol:227:    function _populateParameters(BasicOrder calldata order, OrderExtraData memory orderExtraData)
SignatureChecker.sol:19:    function _splitSignature(bytes memory signature)
SignatureChecker.sol:56:    function _recoverEOASigner(bytes32 hash, bytes memory signature) internal pure returns (address signer) {
interfaces/SeaportInterface.sol:417:    function name() external view returns (string memory contractName);
interfaces/IGemSwap.sol:11:    function batchBuyWithETH(TradeDetails[] memory tradeDetails) external payable;
```

## Use `variable == false|0` -> `!variable` or `variable ==  true` -> `variable`
```solidity
ERC20EnabledLooksRareAggregator.sol:34:        if (tokenTransfers.length == 0) revert UseLooksRareAggregatorDirectly();
proxies/LooksRareProxy.sol:62:        if (ordersLength == 0 || ordersLength != ordersExtraData.length) revert InvalidOrderLength();
proxies/SeaportProxy.sol:72:        if (ordersLength == 0 || ordersLength != ordersExtraData.length) revert InvalidOrderLength();
LooksRareAggregator.sol:60:        if (tradeDataLength == 0) revert InvalidOrderLength();
LooksRareAggregator.sol:63:        if (tokenTransfersLength == 0) {
SignatureChecker.sol:77:        if (signer.code.length == 0) {
TokenRescuer.sol:24:        if (withdrawAmount == 0) revert InsufficientAmount();
TokenRescuer.sol:36:        if (withdrawAmount == 0) revert InsufficientAmount();
```



## ` >=` COSTS LESS GAS THAN `>`
The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde 
```solidity
libraries/OrderStructs.sol:11:  uint256[] amounts; // Always 1 when ERC721, can be > 1 if ERC1155
lowLevelCallers/LowLevelERC20Approve.sol:28:        if (data.length > 0) {
lowLevelCallers/LowLevelERC20Transfer.sol:35:        if (data.length > 0) {
lowLevelCallers/LowLevelERC20Transfer.sol:54:        if (data.length > 0) {
proxies/SeaportProxy.sol:152:                if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
proxies/SeaportProxy.sol:163:        if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
proxies/SeaportProxy.sol:211:                        if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
proxies/SeaportProxy.sol:224:        if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
LooksRareAggregator.sol:92:                    if (returnData.length > 0) {
LooksRareAggregator.sol:108:        if (tokenTransfersLength > 0) _returnERC20TokensIfAny(tokenTransfers, originator);
LooksRareAggregator.sol:158:        if (bp > 10000) revert FeeTooHigh();
LooksRareAggregator.sol:245:            if (balance > 0) _executeERC20DirectTransfer(tokenTransfers[i].currency, recipient, balance);
SignatureChecker.sol:46:        if (uint256(s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0) revert BadSignatureS();
```

