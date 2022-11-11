Overall gas change: -966652 (-22.026%) when running all tests
Overall gas change: -835138 (-14.807%) when running the .*Benchmark files

Marking the function with the onlyOwner modifier as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided cost an average of about 21 gas per call to the function, in addition to the extra deployment cost.

Using uint256 instead of boolean type reduces gas cost further.

```diff
diff --git a/contracts/LooksRareAggregator.sol b/contracts/LooksRareAggregator.sol
index f215f39..0594d2d 100644
--- a/contracts/LooksRareAggregator.sol
+++ b/contracts/LooksRareAggregator.sol
@@ -42,7 +42,7 @@ contract LooksRareAggregator is
      *         aggregator.
      */
     address public erc20EnabledLooksRareAggregator;
-    mapping(address => mapping(bytes4 => bool)) private _proxyFunctionSelectors;
+    mapping(address => mapping(bytes4 => uint256)) private _proxyFunctionSelectors;
     mapping(address => FeeData) private _proxyFeeData;
 
     /**
@@ -68,7 +68,7 @@ contract LooksRareAggregator is
 
         for (uint256 i; i < tradeDataLength; ) {
             TradeData calldata singleTradeData = tradeData[i];
-            if (!_proxyFunctionSelectors[singleTradeData.proxy][singleTradeData.selector]) revert InvalidFunction();
+            if (_proxyFunctionSelectors[singleTradeData.proxy][singleTradeData.selector] == 0) revert InvalidFunction();
 
             (bytes memory proxyCalldata, bool maxFeeBpViolated) = _encodeCalldataAndValidateFeeBp(
                 singleTradeData,
@@ -117,7 +117,7 @@ contract LooksRareAggregator is
      *      a malicious aggregator from being set in case of an ownership compromise.
      * @param _erc20EnabledLooksRareAggregator The ERC20 enabled LooksRare aggregator's address
      */
-    function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external onlyOwner {
+    function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external payable onlyOwner {
         if (erc20EnabledLooksRareAggregator != address(0)) revert AlreadySet();
         erc20EnabledLooksRareAggregator = _erc20EnabledLooksRareAggregator;
         emit ERC20EnabledLooksRareAggregatorSet();
@@ -129,8 +129,8 @@ contract LooksRareAggregator is
      * @param proxy The marketplace proxy's address
      * @param selector The marketplace proxy's function selector
      */
-    function addFunction(address proxy, bytes4 selector) external onlyOwner {
-        _proxyFunctionSelectors[proxy][selector] = true;
+    function addFunction(address proxy, bytes4 selector) external payable onlyOwner {
+        _proxyFunctionSelectors[proxy][selector] = 1;
         emit FunctionAdded(proxy, selector);
     }
 
@@ -140,7 +140,7 @@ contract LooksRareAggregator is
      * @param proxy The marketplace proxy's address
      * @param selector The marketplace proxy's function selector
      */
-    function removeFunction(address proxy, bytes4 selector) external onlyOwner {
+    function removeFunction(address proxy, bytes4 selector) external payable onlyOwner {
         delete _proxyFunctionSelectors[proxy][selector];
         emit FunctionRemoved(proxy, selector);
     }
@@ -154,7 +154,7 @@ contract LooksRareAggregator is
         address proxy,
         uint256 bp,
         address recipient
-    ) external onlyOwner {
+    ) external payable onlyOwner {
         if (bp > 10000) revert FeeTooHigh();
         _proxyFeeData[proxy].bp = bp;
         _proxyFeeData[proxy].recipient = recipient;
@@ -172,7 +172,7 @@ contract LooksRareAggregator is
         address marketplace,
         address currency,
         uint256 amount
-    ) external onlyOwner {
+    ) external payable onlyOwner {
         _executeERC20Approve(currency, marketplace, amount);
     }
 
@@ -181,7 +181,7 @@ contract LooksRareAggregator is
      * @param selector The marketplace proxy's function selector
      * @return Whether the marketplace proxy's function can be called from the aggregator
      */
-    function supportsProxyFunction(address proxy, bytes4 selector) external view returns (bool) {
+    function supportsProxyFunction(address proxy, bytes4 selector) external view returns (uint256) {
         return _proxyFunctionSelectors[proxy][selector];
     }
 
@@ -196,7 +196,7 @@ contract LooksRareAggregator is
         address collection,
         address to,
         uint256 tokenId
-    ) external onlyOwner {
+    ) external payable onlyOwner {
         _executeERC721TransferFrom(collection, address(this), to, tokenId);
     }
 
@@ -213,7 +213,7 @@ contract LooksRareAggregator is
         address to,
         uint256[] calldata tokenIds,
         uint256[] calldata amounts
-    ) external onlyOwner {
+    ) external payable onlyOwner {
         _executeERC1155SafeBatchTransferFrom(collection, address(this), to, tokenIds, amounts);
     }
 
diff --git a/contracts/OwnableTwoSteps.sol b/contracts/OwnableTwoSteps.sol
index 99d8a7d..3354262 100644
--- a/contracts/OwnableTwoSteps.sol
+++ b/contracts/OwnableTwoSteps.sol
@@ -48,7 +48,7 @@ abstract contract OwnableTwoSteps is IOwnableTwoSteps {
      * @dev This function can be used for both cancelling a transfer to a new owner and
      *      cancelling the renouncement of the ownership.
      */
-    function cancelOwnershipTransfer() external onlyOwner {
+    function cancelOwnershipTransfer() external payable onlyOwner {
         if (ownershipStatus == Status.NoOngoingTransfer) revert NoOngoingTransferInProgress();
 
         if (ownershipStatus == Status.TransferInProgress) {
@@ -65,7 +65,7 @@ abstract contract OwnableTwoSteps is IOwnableTwoSteps {
     /**
      * @notice Confirm ownership renouncement
      */
-    function confirmOwnershipRenouncement() external onlyOwner {
+    function confirmOwnershipRenouncement() external payable onlyOwner {
         if (ownershipStatus != Status.RenouncementInProgress) revert RenouncementNotInProgress();
         if (block.timestamp < earliestOwnershipRenouncementTime) revert RenouncementTooEarly();
 
@@ -95,7 +95,7 @@ abstract contract OwnableTwoSteps is IOwnableTwoSteps {
      * @notice Initiate transfer of ownership to a new owner
      * @param newPotentialOwner New potential owner address
      */
-    function initiateOwnershipTransfer(address newPotentialOwner) external onlyOwner {
+    function initiateOwnershipTransfer(address newPotentialOwner) external payable onlyOwner {
         if (ownershipStatus != Status.NoOngoingTransfer) revert TransferAlreadyInProgress();
 
         ownershipStatus = Status.TransferInProgress;
@@ -107,7 +107,7 @@ abstract contract OwnableTwoSteps is IOwnableTwoSteps {
     /**
      * @notice Initiate ownership renouncement
      */
-    function initiateOwnershipRenouncement() external onlyOwner {
+    function initiateOwnershipRenouncement() external payable onlyOwner {
         if (ownershipStatus != Status.NoOngoingTransfer) revert TransferAlreadyInProgress();
 
         ownershipStatus = Status.RenouncementInProgress;
diff --git a/contracts/TokenRescuer.sol b/contracts/TokenRescuer.sol
index f48d65e..4039e51 100644
--- a/contracts/TokenRescuer.sol
+++ b/contracts/TokenRescuer.sol
@@ -19,7 +19,7 @@ contract TokenRescuer is OwnableTwoSteps, LowLevelETH, LowLevelERC20Transfer {
      * @dev Must be called by the current owner
      * @param to Send the contract's ETH balance to this address
      */
-    function rescueETH(address to) external onlyOwner {
+    function rescueETH(address to) external payable onlyOwner {
         uint256 withdrawAmount = address(this).balance - 1;
         if (withdrawAmount == 0) revert InsufficientAmount();
         _transferETH(to, withdrawAmount);
@@ -31,7 +31,7 @@ contract TokenRescuer is OwnableTwoSteps, LowLevelETH, LowLevelERC20Transfer {
      * @param currency The address of the ERC20 token to rescue from the contract
      * @param to Send the contract's specified ERC20 token balance to this address
      */
-    function rescueERC20(address currency, address to) external onlyOwner {
+    function rescueERC20(address currency, address to) external payable onlyOwner {
         uint256 withdrawAmount = IERC20(currency).balanceOf(address(this)) - 1;
         if (withdrawAmount == 0) revert InsufficientAmount();
         _executeERC20DirectTransfer(currency, to, withdrawAmount);
diff --git a/test/foundry/LooksRareAggregator.t.sol b/test/foundry/LooksRareAggregator.t.sol
index 0cbfbfa..a79ba36 100644
--- a/test/foundry/LooksRareAggregator.t.sol
+++ b/test/foundry/LooksRareAggregator.t.sol
@@ -55,11 +55,11 @@ contract LooksRareAggregatorTest is TestParameters, TestHelpers, TokenRescuerTes
     }
 
     function testAddFunction() public {
-        assertTrue(!aggregator.supportsProxyFunction(address(looksRareProxy), LooksRareProxy.execute.selector));
+        assertTrue(aggregator.supportsProxyFunction(address(looksRareProxy), LooksRareProxy.execute.selector) == 0);
         vm.expectEmit(true, true, false, true);
         emit FunctionAdded(address(looksRareProxy), LooksRareProxy.execute.selector);
         aggregator.addFunction(address(looksRareProxy), LooksRareProxy.execute.selector);
-        assertTrue(aggregator.supportsProxyFunction(address(looksRareProxy), LooksRareProxy.execute.selector));
+        assertTrue(aggregator.supportsProxyFunction(address(looksRareProxy), LooksRareProxy.execute.selector) == 1);
     }
 
     function testAddFunctionNotOwner() public {
@@ -69,14 +69,14 @@ contract LooksRareAggregatorTest is TestParameters, TestHelpers, TokenRescuerTes
     }
 
     function testRemoveFunction() public {
-        assertTrue(!aggregator.supportsProxyFunction(address(looksRareProxy), LooksRareProxy.execute.selector));
+        assertTrue(aggregator.supportsProxyFunction(address(looksRareProxy), LooksRareProxy.execute.selector) == 0);
         aggregator.addFunction(address(looksRareProxy), LooksRareProxy.execute.selector);
-        assertTrue(aggregator.supportsProxyFunction(address(looksRareProxy), LooksRareProxy.execute.selector));
+        assertTrue(aggregator.supportsProxyFunction(address(looksRareProxy), LooksRareProxy.execute.selector) == 1);
 
         vm.expectEmit(true, true, false, true);
         emit FunctionRemoved(address(looksRareProxy), LooksRareProxy.execute.selector);
         aggregator.removeFunction(address(looksRareProxy), LooksRareProxy.execute.selector);
-        assertTrue(!aggregator.supportsProxyFunction(address(looksRareProxy), LooksRareProxy.execute.selector));
+        assertTrue(aggregator.supportsProxyFunction(address(looksRareProxy), LooksRareProxy.execute.selector) == 0);
     }
 
     function testRemoveFunctionNotOwner() public {
```