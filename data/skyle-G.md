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

## Use `calldata` instead of `memory` for function arguments that do not get mutated.
Mark data types as `calldata` instead of `memory` where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as `calldata`. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies `memory` storage. 

### Lines
- SeaportProxy.sol:227
- SignatureChecker.sol:19
- SignatureChecker.sol:56
- SignatureChecker.sol:75
- LooksRareProxy.sol:108
- LooksRareProxy.sol:109