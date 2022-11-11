# 1. Cache `orderExtraData.recipients[j]` on `SeaportProxy._populateParameters`

### Code changes

```diff
diff --git a/contracts/proxies/SeaportProxy.sol b/contracts/proxies/SeaportProxy.sol
index ab06a83..246d502 100644
--- a/contracts/proxies/SeaportProxy.sol
+++ b/contracts/proxies/SeaportProxy.sol
@@ -253,11 +253,12 @@ contract SeaportProxy is IProxy, TokenRescuer {
 
         ConsiderationItem[] memory consideration = new ConsiderationItem[](recipientsLength);
         for (uint256 j; j < recipientsLength; ) {
+            AdditionalRecipient memory recipient = orderExtraData.recipients[j];
             // We don't need to assign value to identifierOrCriteria as it is always 0.
-            uint256 recipientAmount = orderExtraData.recipients[j].amount;
+            uint256 recipientAmount = recipient.amount;
             consideration[j].startAmount = recipientAmount;
             consideration[j].endAmount = recipientAmount;
-            consideration[j].recipient = payable(orderExtraData.recipients[j].recipient);
+            consideration[j].recipient = payable(recipient.recipient);
             consideration[j].itemType = order.currency == address(0) ? ItemType.NATIVE : ItemType.ERC20;
             consideration[j].token = order.currency;
```

### Gas changes

```
$ FOUNDRY_PROFILE=local forge snapshot --diff
// ...
Overall gas change: -61072 (-3.277%)
```

# 2. Cache `singleTradeData.proxy` on `LooksRareAggregator.execute`

### Code changes

```diff
diff --git a/contracts/LooksRareAggregator.sol b/contracts/LooksRareAggregator.sol
index f215f39..2ade000 100644
--- a/contracts/LooksRareAggregator.sol
+++ b/contracts/LooksRareAggregator.sol
@@ -68,7 +68,8 @@ contract LooksRareAggregator is
 
         for (uint256 i; i < tradeDataLength; ) {
             TradeData calldata singleTradeData = tradeData[i];
-            if (!_proxyFunctionSelectors[singleTradeData.proxy][singleTradeData.selector]) revert InvalidFunction();
+            address proxy = singleTradeData.proxy;
+            if (!_proxyFunctionSelectors[proxy][singleTradeData.selector]) revert InvalidFunction();
 
             (bytes memory proxyCalldata, bool maxFeeBpViolated) = _encodeCalldataAndValidateFeeBp(
                 singleTradeData,
@@ -85,7 +86,7 @@ contract LooksRareAggregator is
                     continue;
                 }
             }
-            (bool success, bytes memory returnData) = singleTradeData.proxy.delegatecall(proxyCalldata);
+            (bool success, bytes memory returnData) = proxy.delegatecall(proxyCalldata);
 
             if (!success) {
                 if (isAtomic) {
```

### Gas changes

```
$ FOUNDRY_PROFILE=local forge snapshot --diff
// ...
Overall gas change: -96598 (-5.302%)
```