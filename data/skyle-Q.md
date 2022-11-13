## ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard. 

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard. To account for this, either use OpenZeppelin's SafeERC20 library or wrap each operation in a require statement.
Additionally, ERC20's approve functions have a known race-condition vulnerability. To account for this, use OpenZeppelin's SafeERC20 library's `safeIncrease` or `safeDecrease` Allowance functions.

    
### Lines
- LowLevelERC20Transfer.sol:31
- LowLevelERC20Transfer.sol:51
- LowLevelERC721Transfer.sol:27
- TokenTransferrer.sol:22
- LowLevelERC20Approve.sol:25


## A magic number should be documented and explained. Use a constant instead
Consider using constant variables as this would make the code more maintainable and readable while costing nothing gas-wise (constants are replaced by their value at compile-time).
### 
LooksRareAggregator.sol line 158

## Lack of event emission for operation changing the state

There is lack of events for proxy order execution. for example, if the atomic equals false, there will be no event emitting for failure/success order execution

###
LooksRareProxy.sol line 130
SeaportProxy.sol line 206
TokenRescuer.sol line 25
TokenRescuer.sol line 34

## TokenRescuer might not fully rescue the tokens in the contract

the TokenRescuer line 23 requires ETH > 1. If the rescuer could not rescue balance less than 1. Given the price of ETH > $1300 at present, this would trap 1 ETH (over 1 thousand doallor)  in the contract

###
TokenRescuer.sol line 23
TokenRescuer.sol line 35

