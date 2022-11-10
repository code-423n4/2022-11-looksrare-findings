# LooksRare Aggregator Low Risk and Non-Critical Issues
## Summary
| Risk      | Title | File | Instances
| ----------- | ----------- | ----------- | ----------- |
| L-00      | Children of OwnableTwoSteps should set delay | OwnableTwoSteps.sol | - |
| N-00      | Unlocked pragma | - | 11 |
| N-01      | Different compiler versions | - | 11 |
| N-02      | Event is missing indexed fields | - | 8 |

## [L-00] Children of OwnableTwoSteps should set delay
In the `OwnableTwoSteps` contract, function `_setupDelayForRenouncingOwnership` it is stated that "This function is expected to be included in the constructor of the contract that inherits this contract.".  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/OwnableTwoSteps.sol#L122](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/OwnableTwoSteps.sol#L122)  

The `OwnableTwoSteps` contract is inherited by `TokenRescuer` which in turn is inherited by `LooksRareProxy`, `SeaportProxy` and `LooksRareAggregator`.  
None of the children calls the `_setupDelayForRenouncingOwnership` function in their constructor.  

This means there is no timelock to renounce ownership.

## [N-00] Unlocked pragma
It is considered best practice to use a locked Solidity version, thereby only allowing compilation with a specific version.

There are 11 instances of this.  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/ReentrancyGuard.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/ReentrancyGuard.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/OwnableTwoSteps.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/OwnableTwoSteps.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/SignatureChecker.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/SignatureChecker.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L4](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L4)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelETH.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelETH.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC20.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC20.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC721.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC721.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC1155.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC1155.sol#L2)  




## [N-01] Different compiler versions
Across the repository, there are 3 different pragmas used:  
`0.8.17`, `^0.8.14` and `^0.8.0`.  
While all three can be compiled with the same Solidity version (0.8.17), it is best practice to use a single pragme consistenly in all files.  
So consider using `0.8.17` for all files.  

There are 11 instances of files that use a different pragma:

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/ReentrancyGuard.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/ReentrancyGuard.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/OwnableTwoSteps.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/OwnableTwoSteps.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/SignatureChecker.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/SignatureChecker.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L4](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L4)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelETH.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelETH.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC20.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC20.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC721.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC721.sol#L2)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC1155.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC1155.sol#L2)  

## [N-02] Event is missing indexed fields
Each event should use three indexed fields if it has three or more fields.  

There are 8 instances of events that do not have 3 indexed fields.  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/ILooksRareAggregator.sol#L44](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/ILooksRareAggregator.sol#L44)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/ILooksRareAggregator.sol#L51](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/ILooksRareAggregator.sol#L51)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/ILooksRareAggregator.sol#L58](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/ILooksRareAggregator.sol#L58)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC20.sol#L5](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC20.sol#L5)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC20.sol#L7](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC20.sol#L7)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC721.sol#L9](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC721.sol#L9)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC1155.sol#L15](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC1155.sol#L15)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC1155.sol#L17](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC1155.sol#L17)  

