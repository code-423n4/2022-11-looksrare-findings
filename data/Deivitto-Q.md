# QA

# Low 
## Missing checks for address(0x0) when assigning values to `address` state or `immutable` variables 
### Summary
Zero address should be checked for state variables, immutable variables. A zero address can lead into problems such as reverting when calling functions or loss of funds.
### Github Permalinks
- `execute` would fail if `0x00` assigned
https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/ERC20EnabledLooksRareAggregator.sol#L21-L23
- `execute` would fail if `agreggator == 0x00` assigned, 
- `_executeSingleOrder` would fail if `marketplace = 0x00`, then `_transferTokenToRecipient` won't be called, making imposible to transfer tokens to recipient
https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/LooksRareProxy.sol#L37-L40

- `execute` would fail if `agreggator == 0x00` assigned, 
https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/SeaportProxy.sol#L45-L48

### Mitigation
Check zero address before assigning or using it



## block.timestamp used as time proxy 
### Summary
Risk of using `block.timestamp` for time should be considered. 
### Details
`block.timestamp` is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.
### References
SWC ID: 116

### Github Permalinks 


https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/OwnableTwoSteps.sol#L70
        if (block.timestamp < earliestOwnershipRenouncementTime) revert RenouncementTooEarly();

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/OwnableTwoSteps.sol#L114
        earliestOwnershipRenouncementTime = block.timestamp + delay;


### Mitigation
- Consider the risk of using `block.timestamp` as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 
- Consider using an oracle for precision


# Informational
## Maximum line length exceeded
### Summary
Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details 
Lines that exceed the 120 character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length
### Github Permalinks 
https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/interfaces/IERC1155.sol#L5

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/SeaportProxy.sol#L8

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/SeaportProxy.sol#L27

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/SeaportProxy.sol#L35

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/SeaportProxy.sol#L37

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/OwnableTwoSteps.sol#L8

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/OwnableTwoSteps.sol#L9

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/SignatureChecker.sol#L9

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/lowLevelCallers/LowLevelETH.sol#L52
### Mitigation
Reduce line length to less than 120 at least to improve maintainability and readability of the code 



##  Use of magic numbers is confusing and risky
### Summary
Magic numbers are hardcoded numbers used in the code which are ambiguous to their intended purpose. These should be replaced with constants to make code more readable and maintainable.
### Details
Values are hardcoded and would be more readable and maintainable if declared as a constant

### Github Permalinks
https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/SeaportProxy.sol#L147
            uint256 orderFee = (orders[i].price * feeBp) / 10000;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/SeaportProxy.sol#L207
                    uint256 orderFee = (price * feeBp) / 10000;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/LooksRareAggregator.sol#L158
        if (bp > 10000) revert FeeTooHigh();


## Missing indexed event parameters
### Summary
Events without indexed event parameters make it harder and
inefficient for off-chain tools to analyze them.
### Details
Indexed parameters (“topics”) are searchable event parameters.
They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
 
### Github Permalinks


https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/interfaces/ILooksRareAggregator.sol#L36
    event ERC20EnabledLooksRareAggregatorSet();

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/interfaces/ILooksRareAggregator.sol#L44
    event FeeUpdated(address proxy, uint256 bp, address recipient);
### Mitigation
Consider which event parameters could be particularly useful to off-chain tools and should be indexed.





## Different versions of pragma
### Summary
Some of the contracts include an unlocked pragma, e.g., pragma solidity >=0.8.4. 

Locking the pragma helps ensure that contracts are not accidentally deployed using an old compiler version with unfixed bugs.

### Github Permalinks

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/interfaces/IERC20.sol#L2
pragma solidity ^0.8.0;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/interfaces/IERC721.sol#L2
pragma solidity ^0.8.0;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/interfaces/IERC1155.sol#L2
pragma solidity ^0.8.0;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/ReentrancyGuard.sol#L2
pragma solidity ^0.8.14;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/OwnableTwoSteps.sol#L2
pragma solidity ^0.8.14;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/SignatureChecker.sol#L2
pragma solidity ^0.8.14;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L2
pragma solidity ^0.8.14;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L4
pragma solidity ^0.8.14;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L2
pragma solidity ^0.8.14;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L2
pragma solidity ^0.8.14;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/lowLevelCallers/LowLevelETH.sol#L2
pragma solidity ^0.8.14;


### Mitigation
Lock pragmas to a specific Solidity version. 
Consider converting ^0.8.14 into 0.8.17
Consider converting ^0.8.0 into 0.8.17

## Large multiples of ten should use scientific notation (e.g. 1e6) rather than decimal literals (e.g. 1000000), for readability
### Summary
Multiples of 10 can be declared as constants with scientific notation so it's easier to read them and less prone to miss/exceed a 0 of the expected value.

### Details
Values 1000000000000000000 and 1000000000 can be used in scientific notation
### Github Permalinks
https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/SeaportProxy.sol#L147
            uint256 orderFee = (orders[i].price * feeBp) / 10000;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/proxies/SeaportProxy.sol#L207
                    uint256 orderFee = (price * feeBp) / 10000;

https://github.com/code-423n4/2022-11-looksrare/blob/14865ba6a33ab7e8631e6ef2bd5e98207ca57f2a/contracts/LooksRareAggregator.sol#L158
        if (bp > 10000) revert FeeTooHigh();


### Mitigation
Replace hardcoded numbers with constants that represent the scientific corresponding notation


