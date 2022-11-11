# 1. Use `_` to separate decimals on integer literals for better clarity and readability of the code

The change below makes it easier to understand that 10000 is 100_00 and means 100%

```diff
diff --git a/contracts/LooksRareAggregator.sol b/contracts/LooksRareAggregator.sol
index f215f39..2556384 100644
--- a/contracts/LooksRareAggregator.sol
+++ b/contracts/LooksRareAggregator.sol
@@ -155,7 +155,7 @@ contract LooksRareAggregator is
         uint256 bp,
         address recipient
     ) external onlyOwner {
-        if (bp > 10000) revert FeeTooHigh();
+        if (bp > 100_00) revert FeeTooHigh();
         _proxyFeeData[proxy].bp = bp;
         _proxyFeeData[proxy].recipient = recipient;

```