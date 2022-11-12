
# QA

| | issue |
| ----------- | ----------- |
| 1 | [use of floating pragma](#1-use-of-floating-pragma) |
| 2 | [event is missing indexed fields](#2-event-is-missing-indexed-fields) |
| 3 | [lines are too long](#3-lines-are-too-long) |
| 4 | [Scoped contracts are missing proper NatSpec comments](#4-scoped-contracts-are-missing-proper-natspec-comments) |
| 5 | [Unused/empty receive()/fallback() function](#5-unusedempty-receivefallback-function) |
| 6 | [using different Solidity Pragma in different contracts](#6-using-different-solidity-pragma-in-different-contracts) |
| 7 | [Use Underscores for Number Literals](#7-use-underscores-for-number-literals) |


## 1. use of floating pragma
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

- [ReentrancyGuard.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ReentrancyGuard.sol#L)
- [OwnableTwoSteps.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L)
- [SignatureChecker.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L)
- [LowLevelETH.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol#L)
- [LowLevelERC20Approve.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L)
- [LowLevelERC20Transfer.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L)
- [LowLevelERC721Transfer.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L)
- [LowLevelERC1155Transfer.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L)


## 2. event is missing indexed fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

- [IERC20.sol#L5](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20.sol#L5)
- [IERC20.sol#L7](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20.sol#L7)

- [ILooksRareAggregator.sol#L44](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol#L44)


## 3. lines are too long

Usually lines in source code are limited to 80 characters. Its advised to keep lines lower than 120 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

- [SeaportProxy.sol#L35](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L35)
- [SeaportProxy.sol#L37](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L37)



## 4. Scoped contracts are missing proper NatSpec comments
The scoped contracts are missing proper NatSpec comments such as @notice @dev @param on many places. It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI)

- [SeaportProxy.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L)

- [ERC20EnabledLooksRareAggregator.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L)


## 5. Unused/empty receive()/fallback() function
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds

- [SeaportProxy.sol#L86](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L86)

- [LooksRareAggregator.sol#L220](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L220)


## 6. using different Solidity Pragma in different contracts
It is better to use one Solidity compiler version across all contracts instead of different versions with different bugs and security checks.

- [ReentrancyGuard.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ReentrancyGuard.sol#L)
- [OwnableTwoSteps.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L)
- [SignatureChecker.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L)
- [LowLevelETH.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol#L)
- [LowLevelERC20Approve.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L)
- [LowLevelERC20Transfer.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L)
- [LowLevelERC721Transfer.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L)
- [LowLevelERC1155Transfer.sol#L](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L)


## 7. Use Underscores for Number Literals
There are multiple occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.

- [SeaportProxy.sol#L207](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L207)

- [LooksRareAggregator.sol#L158](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L158)
