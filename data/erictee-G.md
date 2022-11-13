### [G-01] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
contracts/LooksRareAggregator.sol:L220    receive() external payable {}

contracts/proxies/LooksRareProxy.sol:L132            } catch {}

contracts/proxies/SeaportProxy.sol:L86    receive() external payable {}

contracts/proxies/SeaportProxy.sol:L217            } catch {}

```
### [G-02] Use a more recent version of solidity.


#### Impact
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining 
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.


#### Findings:
```
contracts/interfaces/IERC721.sol:L2      pragma solidity ^0.8.0;

contracts/interfaces/IERC1155.sol:L2      pragma solidity ^0.8.0;

contracts/interfaces/IERC20.sol:L2      pragma solidity ^0.8.0;

```
### [G-03] Functions guaranteed to revert when called by normal users can be declared as payable.


#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.


#### Findings:
```
contracts/LooksRareAggregator.sol:L120    function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external onlyOwner {

contracts/LooksRareAggregator.sol:L132    function addFunction(address proxy, bytes4 selector) external onlyOwner {

contracts/LooksRareAggregator.sol:L143    function removeFunction(address proxy, bytes4 selector) external onlyOwner {

contracts/OwnableTwoSteps.sol:L51    function cancelOwnershipTransfer() external onlyOwner {

contracts/OwnableTwoSteps.sol:L68    function confirmOwnershipRenouncement() external onlyOwner {

contracts/OwnableTwoSteps.sol:L98    function initiateOwnershipTransfer(address newPotentialOwner) external onlyOwner {

contracts/OwnableTwoSteps.sol:L110    function initiateOwnershipRenouncement() external onlyOwner {

contracts/TokenRescuer.sol:L22    function rescueETH(address to) external onlyOwner {

contracts/TokenRescuer.sol:L34    function rescueERC20(address currency, address to) external onlyOwner {

```

### [G-04] ```X += Y``` costs more gas than ```X = X + Y``` for state variables.


#### Impact
Consider changing ```X += Y``` to ```X = X + Y``` to save gas.


#### Findings:
```
contracts/proxies/SeaportProxy.sol:L150                fee += orderFee;

contracts/proxies/SeaportProxy.sol:L209                        fee += orderFee;

```
