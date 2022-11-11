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
Overall gas change: -61072 (-3.277%)
```