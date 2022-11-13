## Use assembly to write storage values
 for example:
 assembly {
            sstore(.., ...)
        }

### Lines
- LooksRareAggregator.sol:122
- OwnableTwoSteps.sol:43
- OwnableTwoSteps.sol:87
- OwnableTwoSteps.sol:102
- OwnableTwoSteps.sol:114
- OwnableTwoSteps.sol:126
- ReentrancyGuard.sol:15
- ReentrancyGuard.sol:24
- ReentrancyGuard.sol:26

## Use assembly when getting a contract's balance of ETH.

You can use `selfbalance()` instead of `address(this).balance` when getting your contract's balance of ETH to save gas. Additionally, you can use `balance(address)` instead of `address.balance()` when getting an external contract's balance of ETH.

### Lines
- TokenRescuer.sol:23

## Mark functions as payable (with discretion)
You can mark public or external functions as payable to save gas. Functions that are not payable have additional logic to check if there was a value sent with a call, however, making a function payable eliminates this check. This optimization should be carefully considered due to potentially unwanted behavior when a function does not need to accept ether.
### Lines
- LooksRareAggregator.sol:120
- LooksRareAggregator.sol:132
- LooksRareAggregator.sol:143
- LooksRareAggregator.sol:153
- LooksRareAggregator.sol:171
- LooksRareAggregator.sol:184
- LooksRareAggregator.sol:195
- LooksRareAggregator.sol:211
- TokenRescuer.sol:22
- TokenRescuer.sol:34
- TokenReceiver.sol:5
- TokenReceiver.sol:14
- TokenReceiver.sol:24
- OwnableTwoSteps.sol:51
- OwnableTwoSteps.sol:68
- OwnableTwoSteps.sol:83
- OwnableTwoSteps.sol:98
- OwnableTwoSteps.sol:110

## Use `calldata` instead of `memory` for function arguments that do not get mutated.
Mark data types as `calldata` instead of `memory` where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as `calldata`. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies `memory` storage. 

### Lines
- SeaportProxy.sol:227
- SignatureChecker.sol:19
- SignatureChecker.sol:56
- SignatureChecker.sol:75
- LooksRareProxy.sol:108
- LooksRareProxy.sol:109

## Use assembly to check for address(0)
### Lines
- LooksRareAggregator.sol:58
- LooksRareAggregator.sol:121
- SeaportProxy.sol:109
- SeaportProxy.sol:133
- SeaportProxy.sol:171
- SeaportProxy.sol:199
- SeaportProxy.sol:206
- SeaportProxy.sol:261
- SignatureChecker.sol:62