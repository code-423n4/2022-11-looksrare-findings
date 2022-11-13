# LooksRare Aggregator Contest QA Report

## Summary

The following QA issues were found during the code audit:

1. Unsafe ERC20 Operation(s) (1 instance)
2. Unspecific Compiler Version Pragma (3 instances)

Total 4 instances of 2 issues.

## 1. Unsafe ERC20 Operation(s) (1 instance)

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard. It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

```solidity
contracts/TokenTransferrer.sol::22 => IERC721(collection).transferFrom(address(this), recipient, tokenId);
```

## 2. Unspecific Compiler Version Pragma (3 instances)

Avoid floating pragmas for non-library contracts. While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. It is recommended to pin to a concrete compiler version.

```solidity
contracts/OwnableTwoSteps.sol::2 => pragma solidity ^0.8.14;

contracts/ReentrancyGuard.sol::2 => pragma solidity ^0.8.14;

contracts/SignatureChecker.sol::2 => pragma solidity ^0.8.14;
```
