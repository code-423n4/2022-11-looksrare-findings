## The lines are too long

Usually, lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length
There are 2 instances of this issue:

[Line 35](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L35),
[Line 37](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L37)

```
Line 35:    bytes32 zoneHash; // An arbitrary 32-byte value that will be supplied to the zone when fulfilling restricted orders that the zone can utilize when making a determination on whether to authorize the order

Line 37:    bytes32 conduitKey; // A bytes32 value that indicates what conduit, if any, should be utilized as a source for token approvals when performing transfers
```

## Typos

Consider sticking to the proper spelling of words otherwise, it will decrease readability

There is 1 instance of this issue
consider making the following changes:

[Line 244](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/SeaportInterface.sol#L244)

```
Line 244:    *         number of items for offer and consideration along with as set of
Line 245     *         fulfillments allocating offer components to consideration
```

Change "as set of" to "a set of"

```
Line 244:    *         number of items for offer and consideration along with a set of
Line 245     *         fulfillments allocating offer components to consideration
```

## `block.timestamp` is unreliable

Using `block.timestamp` as part of the time checks could be slightly modified by miners/validators to favor them in contracts that contain logic heavily dependent on them.

Consider this problem and warn users that a scenario like this could occur. If the change of timestamps will not affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are 2 instances of this issue:

[Line 70](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L70), [Line 114](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L114)

```
Line 70:    if (block.timestamp < earliestOwnershipRenouncementTime) revert RenouncementTooEarly();

Line 114:    earliestOwnershipRenouncementTime = block.timestamp + delay;
```

## Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

For example:
🤦 Bad:

```
pragma solidity ^0.8.0;
```

🚀 Good:

```
pragma solidity 0.8.4;
```

Here are 13 instances with this issue:

`^0.8.0`

[IERC20.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20.sol),

[IERC721.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC721.sol),

[IERC1155.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC1155.sol)

`^0.8.14`

[LowLevelETH.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol),

[LowLevelERC1155Transfer.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol),

[LowLevelERC721Transfer.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol),

[LowLevelERC20Transfer.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol),

[LowLevelERC20Approve.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol),

[SignatureChecker.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol),

[OwnableTwoSteps.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol),

[ReentrancyGuard.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ReentrancyGuard.sol)

For more info:

[Consensys Audit of 1inch](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#unspecific-compiler-version-pragma)

[Solidity docs](https://docs.soliditylang.org/en/latest/layout-of-source-files.html#version-pragma)
