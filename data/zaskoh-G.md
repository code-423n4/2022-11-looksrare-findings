# use unchecked and payable in contracts/TokenRescuer.sol
TokenRescuer is protected from onlyOwner and so we can add the payable to save some gas.
Also you can use unchecked{} to save even more gas for ETH and ERC20 transfers.

For rescueETH
```bash
-    function rescueETH(address to) external onlyOwner {
-        uint256 withdrawAmount = address(this).balance - 1;
-        if (withdrawAmount == 0) revert InsufficientAmount();
-        _transferETH(to, withdrawAmount);
+    function rescueETH(address to) external payable onlyOwner {
+        uint256 withdrawAmount = address(this).balance;
+        if (withdrawAmount < 2) revert InsufficientAmount();
+        unchecked{_transferETH(to, withdrawAmount - 1);}
    }
```

For rescueERC20
```bash
-    function rescueERC20(address currency, address to) external onlyOwner {
-        uint256 withdrawAmount = IERC20(currency).balanceOf(address(this)) - 1;
-        if (withdrawAmount == 0) revert InsufficientAmount();
-        _executeERC20DirectTransfer(currency, to, withdrawAmount);
+    function rescueERC20(address currency, address to) external payable onlyOwner {
+        uint256 withdrawAmount = IERC20(currency).balanceOf(address(this));
+        if (withdrawAmount < 2) revert InsufficientAmount();
+        unchecked{_executeERC20DirectTransfer(currency, to, withdrawAmount - 1);}
    }
```

Savings (forge snapshot --diff):
```bash
testRescueERC20NotOwner() (gas: -24 (-0.003%))
testRescueERC20NotOwner() (gas: -24 (-0.003%))
testRescueERC20NotOwner() (gas: -24 (-0.003%))
testRescueERC20() (gas: -96 (-0.010%))
testRescueERC20() (gas: -96 (-0.010%))
testRescueERC20() (gas: -96 (-0.010%))
testRescueERC20InsufficientAmount() (gas: -102 (-0.011%))
testRescueERC20InsufficientAmount() (gas: -102 (-0.011%))
testExecuteOrdersLengthMismatch() (gas: -83 (-0.117%))
testExecuteZeroOrders() (gas: -82 (-0.160%))
testRescueETHNotOwner() (gas: -24 (-0.197%))
testRescueETHNotOwner() (gas: -24 (-0.197%))
testRescueETHNotOwner() (gas: -24 (-0.198%))
testExecuteReentrancy() (gas: -14825 (-0.210%))
testRescueETH() (gas: -95 (-0.219%))
testRescueETH() (gas: -95 (-0.219%))
testRescueETH() (gas: -95 (-0.220%))
testExecuteThroughAggregatorTwoOrdersAtomic() (gas: -14825 (-0.260%))
testExecuteThroughAggregatorTwoOrdersNonAtomic() (gas: -14825 (-0.261%))
testExecuteThroughAggregatorSingleOrder() (gas: -14825 (-0.269%))
testBuyFromLooksRareAggregatorTwoNFTsEachMarketplaceAtomic() (gas: -22238 (-0.281%))
testBuyFromLooksRareAggregatorTwoNFTsEachMarketplaceNonAtomic() (gas: -22242 (-0.282%))
testBuyFromLooksRareAggregatorAtomic() (gas: -22242 (-0.296%))
testBuyFromLooksRareAggregatorNonAtomic() (gas: -22242 (-0.298%))
testExecuteThroughAggregatorTwoOrdersNonAtomic() (gas: -14833 (-0.306%))
testExecuteThroughAggregatorTwoOrdersAtomic() (gas: -14833 (-0.306%))
testExecuteZeroOriginator() (gas: -14833 (-0.318%))
testExecuteThroughAggregatorSingleOrder() (gas: -14833 (-0.319%))
testExecuteThroughV0AggregatorTwoOrders() (gas: -14820 (-0.323%))
testExecuteThroughV0AggregatorSingleOrder() (gas: -14824 (-0.336%))
testExecuteThroughV0AggregatorTwoOrders() (gas: -14831 (-0.398%))
testExecuteThroughV0AggregatorSingleOrder() (gas: -14831 (-0.420%))
testRescueETHInsufficientAmount() (gas: -101 (-0.852%))
testRescueETHInsufficientAmount() (gas: -101 (-0.853%))
Overall gas change: -268190 (-8.178%)
```

# use calldata in contracts/TokenReceiver.sol
For the function onERC1155Received and onERC1155BatchReceived you can use calldata instead of memory to save gas.
```bash
 abstract contract TokenReceiver {
     function onERC721Received(
         address,
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
```

Savings (forge snapshot --diff):
```bash
testRescueERC1155() (gas: -326 (-0.025%))
testRescueERC1155NotOwner() (gas: -326 (-0.025%))
testExecuteAtomic() (gas: -326 (-0.130%))
testExecuteNonAtomic() (gas: -326 (-0.130%))
testBuyFromLooksRareAggregatorTwoNFTsEachMarketplaceAtomic() (gas: -83736 (-1.060%))
testBuyFromLooksRareAggregatorTwoNFTsEachMarketplaceNonAtomic() (gas: -83736 (-1.065%))
testBuyFromLooksRareAggregatorAtomic() (gas: -83736 (-1.117%))
testBuyFromLooksRareAggregatorNonAtomic() (gas: -83736 (-1.123%))
testExecuteReentrancy() (gas: -83736 (-1.189%))
testExecuteThroughAggregatorTwoOrdersAtomic() (gas: -83736 (-1.471%))
testExecuteThroughAggregatorTwoOrdersNonAtomic() (gas: -83736 (-1.477%))
testExecuteThroughAggregatorSingleOrder() (gas: -83736 (-1.521%))
testExecuteThroughAggregatorTwoOrdersNonAtomic() (gas: -83794 (-1.735%))
testExecuteThroughAggregatorTwoOrdersAtomic() (gas: -83794 (-1.735%))
testExecuteThroughV0AggregatorTwoOrders() (gas: -80725 (-1.765%))
testExecuteZeroOriginator() (gas: -83794 (-1.805%))
testExecuteThroughAggregatorSingleOrder() (gas: -83794 (-1.809%))
testExecuteThroughV0AggregatorSingleOrder() (gas: -80725 (-1.838%))
testExecuteThroughV0AggregatorTwoOrders() (gas: -80725 (-2.176%))
testExecuteThroughV0AggregatorSingleOrder() (gas: -80725 (-2.296%))
Overall gas change: -1329268 (-25.492%)
```

# change validation check in contracts/SignatureChecker.sol
In _verify (Line 72) you can change the if condition to return early to save some gas.

```bash
         if (signer.code.length == 0) {
-            if (_recoverEOASigner(hash, signature) != signer) revert InvalidSignatureEOA();
+            if (_recoverEOASigner(hash, signature) == signer) return;
+            revert InvalidSignatureEOA();
         } else {
-            if (IERC1271(signer).isValidSignature(hash, signature) != 0x1626ba7e) revert InvalidSignatureERC1271();
+            if (IERC1271(signer).isValidSignature(hash, signature) == 0x1626ba7e) return;
+            revert InvalidSignatureERC1271();
        }
```

Savings (forge snapshot --diff):
```bash
testCannotSignIfWrongSignatureERC1271() (gas: -8 (-0.002%))
testSignERC1271() (gas: -10 (-0.003%))
testSignEOA() (gas: -1 (-0.003%))
testCannotSignIfWrongSignatureEOA() (gas: 1 (0.004%))
Overall gas change: -18 (-0.004%)
```

# change function as payable in contracts/OwnableTwoSteps.sol
As all the functions are not for endusers and only handled by admins it makes sense to add payable to save gas.

```bash
-    function cancelOwnershipTransfer() external onlyOwner {
+    function cancelOwnershipTransfer() external payable onlyOwner {

-    function confirmOwnershipRenouncement() external onlyOwner {
+    function confirmOwnershipRenouncement() external payable onlyOwner {

-    function confirmOwnershipTransfer() external {
+    function confirmOwnershipTransfer() external payable {

-    function initiateOwnershipTransfer(address newPotentialOwner) external onlyOwner {
+    function initiateOwnershipTransfer(address newPotentialOwner) external payable onlyOwner {

-    function initiateOwnershipRenouncement() external onlyOwner {
+    function initiateOwnershipRenouncement() external payable onlyOwner {
```

Savings (forge snapshot --diff):
```bash
testRenounceOwnership() (gas: -39 (-0.063%))
testCannotConfirmRenouncementOwnershipPriorToTimelock() (gas: -48 (-0.066%))
testTransferOwnershipToNewOwner() (gas: -38 (-0.068%))
testCancelTransferOwnership() (gas: -77 (-0.076%))
testWrongRecipientCannotClaim() (gas: -48 (-0.076%))
testCannotRenounceOrInitiateTransferASecondtime() (gas: -134 (-0.153%))
testCannotCancelTransferIfNoTransferInProgress() (gas: -24 (-0.154%))
testExecuteOrdersLengthMismatch() (gas: -166 (-0.234%))
testCannotConfirmOwnershipOrRenouncementTransferIfNotInProgress() (gas: -48 (-0.255%))
testExecuteZeroOrders() (gas: -165 (-0.323%))
testExecuteReentrancy() (gas: -26052 (-0.375%))
testOwnableFunctionsOnlyCallableByOwner(address) (gas: -96 (-0.397%))
testExecuteThroughAggregatorTwoOrdersAtomic() (gas: -26051 (-0.464%))
testExecuteThroughAggregatorTwoOrdersNonAtomic() (gas: -26051 (-0.466%))
testExecuteThroughAggregatorSingleOrder() (gas: -26051 (-0.480%))
testBuyFromLooksRareAggregatorTwoNFTsEachMarketplaceAtomic() (gas: -39062 (-0.500%))
testBuyFromLooksRareAggregatorTwoNFTsEachMarketplaceNonAtomic() (gas: -39071 (-0.502%))
testBuyFromLooksRareAggregatorAtomic() (gas: -39071 (-0.527%))
testBuyFromLooksRareAggregatorNonAtomic() (gas: -39071 (-0.530%))
testExecuteThroughAggregatorTwoOrdersNonAtomic() (gas: -26050 (-0.549%))
testExecuteThroughAggregatorTwoOrdersAtomic() (gas: -26050 (-0.549%))
testExecuteZeroOriginator() (gas: -26050 (-0.571%))
testExecuteThroughAggregatorSingleOrder() (gas: -26050 (-0.573%))
testExecuteThroughV0AggregatorTwoOrders() (gas: -26040 (-0.579%))
testExecuteThroughV0AggregatorSingleOrder() (gas: -26049 (-0.604%))
testExecuteThroughV0AggregatorTwoOrders() (gas: -26047 (-0.718%))
testExecuteThroughV0AggregatorSingleOrder() (gas: -26047 (-0.758%))
Overall gas change: -469746 (-10.612%)
```

# change onlyOwner functions to payable in contracts/LooksRareAggregator.sol
As the onlyOwner functions are not for endusers and only handled by admins it makes sense to add payable to save gas.
```bash
-    function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external onlyOwner {
+    function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external payable onlyOwner {

-    function addFunction(address proxy, bytes4 selector) external onlyOwner {
+    function addFunction(address proxy, bytes4 selector) external payable onlyOwner {

-    function removeFunction(address proxy, bytes4 selector) external onlyOwner {
+    function removeFunction(address proxy, bytes4 selector) external payable onlyOwner {

    function setFee(
        address proxy,
        uint256 bp,
        address recipient
-    ) external onlyOwner {
+    ) external payable onlyOwner {

    function approve(
        address marketplace,
        address currency,
        uint256 amount
-    ) external onlyOwner {
+    ) external payable onlyOwner {

    function rescueERC721(
        address collection,
        address to,
        uint256 tokenId
-    ) external onlyOwner {
+    ) external payable onlyOwner {

    function rescueERC1155(
        address collection,
        address to,
        uint256[] calldata tokenIds,
        uint256[] calldata amounts
-    ) external onlyOwner {
+    ) external payable onlyOwner {
```

Savings (forge snapshot --diff):
```bash
testRescueERC1155() (gas: -24 (-0.002%))
testRescueERC1155NotOwner() (gas: -24 (-0.002%))
testRescueERC721() (gas: -24 (-0.002%))
testRescueERC721NotOwner() (gas: -24 (-0.002%))
testExecuteWithFeesAtomic() (gas: -24 (-0.003%))
testApprove() (gas: -24 (-0.003%))
testApproveNotOwner() (gas: -24 (-0.003%))
testExecuteWithFeesNonAtomic() (gas: -24 (-0.003%))
testSetERC20EnabledLooksRareAggregator() (gas: -24 (-0.003%))
testSetERC20EnabledLooksRareAggregatorNotOwner() (gas: -24 (-0.003%))
testExecuteWithFeesAtomic() (gas: -24 (-0.004%))
testExecuteWithFeesNonAtomic() (gas: -24 (-0.004%))
testSetERC20EnabledLooksRareAggregatorAlreadySet() (gas: -48 (-0.006%))
testExecuteMaxFeeBpViolationAtomic() (gas: -48 (-0.010%))
testExecuteMaxFeeBpViolationNonAtomic() (gas: -48 (-0.011%))
testSetFee() (gas: -24 (-0.042%))
testAddFunction() (gas: -24 (-0.056%))
testRemoveFunction() (gas: -38 (-0.111%))
testRemoveFunctionNotOwner() (gas: -48 (-0.123%))
testSetFeeNotOwner() (gas: -24 (-0.171%))
testAddFunctionNotOwner() (gas: -24 (-0.176%))
testSetFeeTooHigh() (gas: -24 (-0.177%))
testBuyFromLooksRareAggregatorTwoNFTsEachMarketplaceAtomic() (gas: -18279 (-0.235%))
testBuyFromLooksRareAggregatorTwoNFTsEachMarketplaceNonAtomic() (gas: -18279 (-0.236%))
testBuyFromLooksRareAggregatorAtomic() (gas: -18279 (-0.248%))
testBuyFromLooksRareAggregatorNonAtomic() (gas: -18279 (-0.249%))
testExecuteReentrancy() (gas: -18255 (-0.263%))
testExecuteThroughAggregatorTwoOrdersAtomic() (gas: -18255 (-0.327%))
testExecuteThroughAggregatorTwoOrdersNonAtomic() (gas: -18255 (-0.328%))
testExecuteThroughAggregatorSingleOrder() (gas: -18255 (-0.338%))
testExecuteThroughAggregatorTwoOrdersNonAtomic() (gas: -18269 (-0.387%))
testExecuteThroughAggregatorTwoOrdersAtomic() (gas: -18269 (-0.387%))
testExecuteZeroOriginator() (gas: -18269 (-0.403%))
testExecuteThroughAggregatorSingleOrder() (gas: -18269 (-0.404%))
Overall gas change: -219850 (-4.724%)
```

# Remove unused function in contracts/lowLevelCallers/LowLevelETH.sol
To save deployment size and also gas on the function ordering the function _returnETHIfAnyWithOneWeiLeft could be removed. If you want to keep it, think about changing the name so it is ordered after the other functions.
```bash
contracts/lowLevelCallers/LowLevelETH.sol
-    /**
-     * @notice Return ETH to the original sender if any is left in the payable call but leave 1 wei of ETH in the contract.
-     */
-    function _returnETHIfAnyWithOneWeiLeft() internal {
-        assembly {
-            if gt(selfbalance(), 1) {
-                let status := call(gas(), caller(), sub(selfbalance(), 1), 0, 0, 0, 0)
-            }
-        }
-    }

test/foundry/LowLevelETH.t.sol
-    function transferETHAndReturnFundsExceptOneWei() external payable {
-        _returnETHIfAnyWithOneWeiLeft();
-    }

-    function testTransferETHAndReturnFundsExceptOneWei(uint112 amount) external payable asPrankedUser(_sender) {
-        vm.deal(_sender, amount);
-        lowLevelETH.transferETHAndReturnFundsExceptOneWei{value: amount}();
-
-        if (amount > 1) {
-            assertEq(_sender.balance, amount - 1);
-            assertEq(address(lowLevelETH).balance, 1);
-        } else {
-            assertEq(_sender.balance, 0);
-            assertEq(address(lowLevelETH).balance, amount);
-        }
-    }
```

Savings (forge snapshot --diff):
```bash
testTransferETHAndReturnFundsToSpecificAddress(uint112) (gas: -22 (-0.039%))
testTransferETH(address,uint112) (gas: -22 (-0.041%))
Overall gas change: -44 (-0.081%)
```

# Remove unused function in contracts/lowLevelCallers/LowLevelERC1155Transfer.sol
To save deployment size the function _returnETHIfAnyWithOneWeiLeft could be removed.
