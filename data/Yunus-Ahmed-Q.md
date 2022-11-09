The contract [ReentrancyGuard.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ReentrancyGuard.sol)  is vulnerable to **Floating Pragma Vulnerablity**.
Solidity pragma versioning should be exactly same in all contracts. Currently some contracts use ^0.8.14 but some are fixed to 0.8.17.

[Click here to view the ReentrancyGuard smart contract](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ReentrancyGuard.sol) 