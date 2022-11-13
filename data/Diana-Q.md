
## L-01 NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

This issue exists in the following In-scope contracts :

[IERC20](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20.sol), [IERC721](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC721.sol), [IERC1155](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC1155.sol), [LowLevelERC20Approve](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol), [LowLevelERC20Transfer](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol), [LowLevelERC721Transfer](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol), [LowLevelERC1155Transfer](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol), [LowLevelETH](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol), [OwnableTwoSteps](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol), [ReentrancyGuard](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ReentrancyGuard.sol), [SignatureChecker](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol)

### Recommended Mitigation Steps

Lock the pragma version

-------------

## N-01 EVENT IS MISSING INDEXED FIELDS

Each `event` should use three `indexed` fields if there are three or more fields

_There are 7 instances of this issue_

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20.sol

```
File: contracts/interfaces/IERC20.sol

5: event Transfer(address indexed from, address indexed to, uint256 value);
7: event Approval(address indexed owner, address indexed spender, uint256 value);
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC721.sol

```
File: contracts/interfaces/IERC721.sol

9: event ApprovalForAll(address indexed owner, address indexed operator, bool approved);
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC1155.sol

```
File: contracts/interfaces/IERC1155.sol

15: event ApprovalForAll(address indexed account, address indexed operator, bool approved);
17: event URI(string value, uint256 indexed id);
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol

```
File: contracts/interfaces/ILooksRareAggregator.sol

44: event FeeUpdated(address proxy, uint256 bp, address recipient);
51: event FunctionAdded(address indexed proxy, bytes4 selector);
```

------------

## N-02 USE OF block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

_There are 2 instances of this issue_

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol

```
File: contracts/OwnableTwoSteps.sol

70: if (block.timestamp < earliestOwnershipRenouncementTime) revert RenouncementTooEarly();
114: earliestOwnershipRenouncementTime = block.timestamp + delay;
```

------------


