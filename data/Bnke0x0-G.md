
### [G01] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gusset (20000 gas)
#### Findings:
```
2022-11-looksrare/contracts/ERC20EnabledLooksRareAggregator.sol::16 => ILooksRareAggregator public immutable aggregator;
2022-11-looksrare/contracts/LooksRareAggregator.sol::44 => address public erc20EnabledLooksRareAggregator;
2022-11-looksrare/contracts/OwnableTwoSteps.sol::13 => address public owner;
2022-11-looksrare/contracts/OwnableTwoSteps.sol::16 => address public potentialOwner;
2022-11-looksrare/contracts/OwnableTwoSteps.sol::19 => uint256 public delay;
2022-11-looksrare/contracts/OwnableTwoSteps.sol::22 => uint256 public earliestOwnershipRenouncementTime;
2022-11-looksrare/contracts/OwnableTwoSteps.sol::25 => Status public ownershipStatus;
2022-11-looksrare/contracts/ReentrancyGuard.sol::12 => uint256 private _status;
```



### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaper

#### Findings:
```
2022-11-looksrare/contracts/LooksRareAggregator.sol::44 => address public erc20EnabledLooksRareAggregator;
2022-11-looksrare/contracts/OwnableTwoSteps.sol::13 => address public owner;
2022-11-looksrare/contracts/OwnableTwoSteps.sol::16 => address public potentialOwner;
```



### [G03] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::126 => for (uint256 i; i < availableOrders.length; ) {
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::187 => for (uint256 i; i < orders.length; ) {
```




### [G04] Not using the named return variables when a function returns, wastes deployment gas


#### Findings:
```
2022-11-looksrare/contracts/LooksRareAggregator.sol::185 => return _proxyFunctionSelectors[proxy][selector];
```



### [G05] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement


#### Findings:
```
2022-11-looksrare/contracts/LooksRareAggregator.sol::92 => if (returnData.length > 0) {
2022-11-looksrare/contracts/LooksRareAggregator.sol::108 => if (tokenTransfersLength > 0) _returnERC20TokensIfAny(tokenTransfers, originator);
2022-11-looksrare/contracts/LooksRareAggregator.sol::245 => if (balance > 0) _executeERC20DirectTransfer(tokenTransfers[i].currency, recipient
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Approve.sol::28 => if (data.length > 0) {
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Transfer.sol::35 => if (data.length > 0) {
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Transfer.sol::54 => if (data.length > 0) {
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::152 => if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::163 => if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::211 => if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::224 => if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
```



### [G06] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed
#### Findings:
```
2022-11-looksrare/contracts/SignatureChecker.sol::25 => uint8 v
2022-11-looksrare/contracts/SignatureChecker.sol::57 => (bytes32 r, bytes32 s, uint8 v) = _splitSignature(signature);
2022-11-looksrare/contracts/libraries/seaport/ConsiderationStructs.sol::172 => uint120 numerator;
2022-11-looksrare/contracts/libraries/seaport/ConsiderationStructs.sol::173 => uint120 denominator;
2022-11-looksrare/contracts/libraries/seaport/ConsiderationStructs.sol::188 => uint120 numerator;
2022-11-looksrare/contracts/libraries/seaport/ConsiderationStructs.sol::189 => uint120 denominator;
2022-11-looksrare/contracts/proxies/LooksRareProxy.sol::84 => (bytes32 r, bytes32 s, uint8 v) = _splitSignature(order.signature);
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::31 => uint120 numerator; // A fraction to attempt to fill
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::32 => uint120 denominator; // The total size of the order
```



### [G07] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function


#### Findings:
```
2022-11-looksrare/contracts/OwnableTwoSteps.sol::99 => if (ownershipStatus != Status.NoOngoingTransfer) revert TransferAlreadyInProgress();
2022-11-looksrare/contracts/TokenRescuer.sol::24 => if (withdrawAmount == 0) revert InsufficientAmount();
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Approve.sol::27 => if (!status) revert ERC20ApprovalFail();
2022-11-looksrare/contracts/proxies/LooksRareProxy.sol::62 => if (ordersLength == 0 || ordersLength != ordersExtraData.length) revert InvalidOrderLength();
```



### [G08] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

#### Findings:
```
2022-11-looksrare/contracts/LooksRareAggregator.sol::120 => function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external onlyOwner {
2022-11-looksrare/contracts/LooksRareAggregator.sol::132 => function addFunction(address proxy, bytes4 selector) external onlyOwner {
2022-11-looksrare/contracts/LooksRareAggregator.sol::143 => function removeFunction(address proxy, bytes4 selector) external onlyOwner {
2022-11-looksrare/contracts/OwnableTwoSteps.sol::51 => function cancelOwnershipTransfer() external onlyOwner {
2022-11-looksrare/contracts/OwnableTwoSteps.sol::68 => function confirmOwnershipRenouncement() external onlyOwner {
2022-11-looksrare/contracts/OwnableTwoSteps.sol::98 => function initiateOwnershipTransfer(address newPotentialOwner) external onlyOwner {
2022-11-looksrare/contracts/OwnableTwoSteps.sol::110 => function initiateOwnershipRenouncement() external onlyOwner {
2022-11-looksrare/contracts/TokenRescuer.sol::22 => function rescueETH(address to) external onlyOwner {
2022-11-looksrare/contracts/TokenRescuer.sol::34 => function rescueERC20(address currency, address to) external onlyOwner {
```


### [G09] Use `calldata` instead of `memory` for function parameters

#### Impact
Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.
#### Findings:
```
2022-11-looksrare/contracts/SignatureChecker.sol::19 => function _splitSignature(bytes memory signature)
2022-11-looksrare/contracts/SignatureChecker.sol::56 => function _recoverEOASigner(bytes32 hash, bytes memory signature) internal pure returns (address signer) {
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::227 => function _populateParameters(BasicOrder calldata order, OrderExtraData memory orderExtraData)
```




### [G10] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done in some places, it’s not consistently done in the solution.

I suggest adding a non-zero-value check here:
#### Findings:
```
2022-11-looksrare/contracts/TokenTransferrer.sol::22 => IERC721(collection).transferFrom(address(this), recipient, tokenId);
```



### [G11] Using `bools` for storage incurs overhead


#### Findings:
```
2022-11-looksrare/contracts/ERC20EnabledLooksRareAggregator.sol::32 => bool isAtomic
2022-11-looksrare/contracts/LooksRareAggregator.sol::45 => mapping(address => mapping(bytes4 => bool)) private _proxyFunctionSelectors;
2022-11-looksrare/contracts/libraries/seaport/ConsiderationStructs.sol::186 => bool isValidated;
2022-11-looksrare/contracts/libraries/seaport/ConsiderationStructs.sol::187 => bool isCancelled;
2022-11-looksrare/contracts/lowLevelCallers/LowLevelETH.sol::20 => bool status;ethValue}(
```



### [G12] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
2022-11-looksrare/contracts/LooksRareAggregator.sol::220 => receive() external payable {}
2022-11-looksrare/contracts/proxies/LooksRareProxy.sol::132 => } catch {}
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::86 => receive() external payable {}
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::217 => } catch {}
```




### [G13] Optimize names to save gas

#### Impact
public/external function names and public member variable names can be optimized to save gas. See this
 link for an example of how it works. Below are the interfaces/abstract 
contracts that can be optimized so that the most frequently-called 
functions use the least amount of gas possible during method lookup. 
Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, per sorted position shifted
#### Findings:
```
2022-11-looksrare/contracts/OwnableTwoSteps.sol::11 => abstract contract OwnableTwoSteps is IOwnableTwoSteps {
2022-11-looksrare/contracts/ReentrancyGuard.sol::11 => abstract contract ReentrancyGuard is IReentrancyGuard {
2022-11-looksrare/contracts/SignatureChecker.sol::11 => abstract contract SignatureChecker is ISignatureChecker {
2022-11-looksrare/contracts/TokenReceiver.sol::4 => abstract contract TokenReceiver {
2022-11-looksrare/contracts/TokenTransferrer.sol::13 => abstract contract TokenTransferrer {
```
