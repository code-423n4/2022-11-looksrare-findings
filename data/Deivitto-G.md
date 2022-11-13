
# GAS
## Empty blocks should be removed or emit something
### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation.

### Details
If the block is an empty if -statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified ( if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}} )
### Github Permalinks


https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/LooksRareProxy.sol#L132
            } catch {}


https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/SeaportProxy.sol#L217
            } catch {}




### Mitigation
Remove empty blocked or emit something.

## Store using Struct over multiple mappings
### Summary
All these variables could be combine in a Struct in order to reduce the gas cost. 
### Details
As noticed in: 
https://gist.github.com/alexon1234/b101e3ac51bea3cbd9cf06f80eaa5bc2
When multiple mappings that access the same addresses, uints, etc, all of them can be mixed into an struct and then that data accessed like:
mapping(datatype => newStructCreated) newStructMap;
Also, you have this post where it explains the benefits of using Structs over mappings 
https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e

### Github Permalinks


https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/LooksRareAggregator.sol#L45
    mapping(address => mapping(bytes4 => bool)) private _proxyFunctionSelectors;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/LooksRareAggregator.sol#L46
    mapping(address => FeeData) private _proxyFeeData;


- - - 

### Mitigation
Consider mixing different mappings into an struct when able in order to save gas.


## Pack structs tightly
### Summary
Gas efficiency can be achieved by tightly packing the struct. Struct variables are stored in 32 bytes each so you can group smaller types to occupy less storage. 

### Details
You can read more here: https://fravoll.github.io/solidity-patterns/tight_variable_packing.html or in the official documentation: https://docs.soliditylang.org/en/v0.4.21/miscellaneous.html
### Github Permalinks
https://github.com/code-423n4/2022-11-looksrare/blob/3f3217f95beab82ccc4e126acc7622816b345fe9/contracts/libraries/OrderStructs.sol#L7-L16
https://github.com/code-423n4/2022-11-looksrare/blob/3f3217f95beab82ccc4e126acc7622816b345fe9/contracts/libraries/seaport/ConsiderationStructs.sol#L100-L121
### Mitigation
Order structs to reduce gas usage.

## Functions guaranteed to revert when called by normal users can be marked `payable`
### Summary
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function.

Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Details
The extra opcodes avoided are:
CALLVALUE (2), DUP1 (3), ISZERO (3), PUSH2 (3), JUMPI (10), PUSH1 (3), DUP1 (3), REVERT(0), JUMPDEST (1), POP (2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Github Permalinks


https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/LooksRareAggregator.sol#L120
    function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external onlyOwner {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/LooksRareAggregator.sol#L132
    function addFunction(address proxy, bytes4 selector) external onlyOwner {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/LooksRareAggregator.sol#L143
    function removeFunction(address proxy, bytes4 selector) external onlyOwner {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/LooksRareAggregator.sol#L157
    ) external onlyOwner {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/LooksRareAggregator.sol#L175
    ) external onlyOwner {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/LooksRareAggregator.sol#L199
    ) external onlyOwner {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/LooksRareAggregator.sol#L216
    ) external onlyOwner {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/TokenRescuer.sol#L22
    function rescueETH(address to) external onlyOwner {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/TokenRescuer.sol#L34
    function rescueERC20(address currency, address to) external onlyOwner {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/OwnableTwoSteps.sol#L51
    function cancelOwnershipTransfer() external onlyOwner {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/OwnableTwoSteps.sol#L68
    function confirmOwnershipRenouncement() external onlyOwner {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/OwnableTwoSteps.sol#L98
    function initiateOwnershipTransfer(address newPotentialOwner) external onlyOwner {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/OwnableTwoSteps.sol#L110
    function initiateOwnershipRenouncement() external onlyOwner {





### Mitigation
Consider adding payable to save gas


## `>=` cheaper than `>`
### Summary
Strict inequalities ( `>` ) are more expensive than non-strict ones ( `>=` ). This is due to some supplementary checks (`ISZERO`, 3 gas)

### Github Permalinks


https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/SeaportProxy.sol#L152
                if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/SeaportProxy.sol#L163
        if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/SeaportProxy.sol#L211
                        if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/SeaportProxy.sol#L224
        if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/LooksRareAggregator.sol#L92
                    if (returnData.length > 0) {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/LooksRareAggregator.sol#L108
        if (tokenTransfersLength > 0) _returnERC20TokensIfAny(tokenTransfers, originator);

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/LooksRareAggregator.sol#L245
            if (balance > 0) _executeERC20DirectTransfer(tokenTransfers[i].currency, recipient, balance);


https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L28
        if (data.length > 0) {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L35
        if (data.length > 0) {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L54
        if (data.length > 0) {


### Mitigation
Consider using `>= 1` instead of `> 0` to avoid some opcodes

## Internal functions only called once can be inlined to save gas
### Summary
Not inlining costs `20` to `40` gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.
### Github Permalinks


https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/OwnableTwoSteps.sol#L125
    function _setupDelayForRenouncingOwnership(uint256 _delay) internal {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/SignatureChecker.sol#L56
    function _recoverEOASigner(bytes32 hash, bytes memory signature) internal pure returns (address signer) {

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/lowLevelCallers/LowLevelETH.sol#L54
    function _returnETHIfAnyWithOneWeiLeft() internal {



### Mitigation
Consider changing internal function only called once to inline code for gas savings


## Retrieve internal variables directly
### Summary
Save `~30 gas` per call to each function

### Example
return sharesOf(account).mul(pricePerShare()).div(1e18);
to
return _balances[account].mul(pricePerShare()).div(1e18);
https://github.com/code-423n4/2021-10-badgerdao-findings/issues/19

### Github Permalinks
0xInternalRetrieve


### Mitigation
Retrieve internal variables directly rather than calling the retrieving function





## Unnecesary storage read
### Summary
A value which value is already known can be used directly rather than reading it from the storage
### Example
```
    function confirmOwnershipTransfer() external {
        if (ownershipStatus != Status.TransferInProgress) revert TransferNotInProgress();
        if (msg.sender != potentialOwner) revert WrongPotentialOwner();

        owner = msg.sender;
        delete ownershipStatus;
        delete potentialOwner;

        emit NewOwner(owner);
    }
```

Reading owner again can be both omitted by calling msg.sender or by storing into a variable 

Recommendation
Change to:

```
    function confirmOwnershipTransfer() external {
        if (ownershipStatus != Status.TransferInProgress) revert TransferNotInProgress();
        address caller = msg.sender; @audit caching variable and then using in emit
        if (caller != potentialOwner) revert WrongPotentialOwner();
        
        owner = //@audit or msg.sender;
        delete ownershipStatus;
        delete potentialOwner;

        emit NewOwner(caller);
    }
```

### Github Permalinks
https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/OwnableTwoSteps.sol#L91
        emit NewOwner(owner);

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/OwnableTwoSteps.sol#L104
        emit InitiateOwnershipTransfer(owner, newPotentialOwner);

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/OwnableTwoSteps.sol#L116
        emit InitiateOwnershipRenouncement(earliestOwnershipRenouncementTime);


### Mitigation
Set directly the value to avoid unnecessary storage read to save some gas

