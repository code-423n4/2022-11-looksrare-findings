### N-01 FLOATING PRAGMA VERSION SHOULD NOT BE USED

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

This applies to all the contracts that are using floating pragma version such as :

```
pragma solidity ^0.8.14;
```

### Proof of Concept

[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)


### N-01 EVENT IS MISSING INDEXED FIELDS

*Number of Instances Identified: 7*

Each `event` should use three `indexed` fields if there are three or more fields

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20.sol

```
5: event Transfer(address indexed from, address indexed to, uint256 value);
7: event Approval(address indexed owner, address indexed spender, uint256 value);
```


[https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC721.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC721.sol)

```
9: event ApprovalForAll(address indexed owner, address indexed operator, bool approved);
```


https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC1155.sol

```
15: event ApprovalForAll(address indexed account, address indexed operator, bool approved);
17: event URI(string value, uint256 indexed id);
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol

```
44: event FeeUpdated(address proxy, uint256 bp, address recipient);
51: event FunctionAdded(address indexed proxy, bytes4 selector);
```


### N-02 USE OF block.timestamp

*Number of Instances Identified: 2*

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol

```
70: if (block.timestamp < earliestOwnershipRenouncementTime) revert RenouncementTooEarly();
114: earliestOwnershipRenouncementTime = block.timestamp + delay;
```