## Unspecific Pragma Version

e.g. `pragma solidity ^0.8.0;` is very unspecific.

Locking the pragma helps ensure that contracts don't get deployed with unintended versions, for example, the latest compiler which could have higher risks of undiscovered bugs.

## Contract owner has too many privileges

The owner of the contract has too many privileges compared to standard users. The result is disastrous if the contract owner's private key gets compromised. And if the key is lost, no updates/upgrades will ever be possible. Moreover, an attacker would be more likely to target the owner because of how many privileges they have.

Consider:

1. Splitting privileges to ensure that no address has too much ownership of the system.
2. Document the functions and implementations the owner can change.
3. Ensuring that all users know of all the dangers associated with the system.

## Typos

File: `SeaportInterface.sol` [Line 244](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/SeaportInterface.sol#L244)

```
/// @audit Change "as set of" to "a set of"
244     *         number of items for offer and consideration along with as set of
245     *         fulfillments allocating offer components to consideration
```

## Not fully using OpenZeppelin contracts

Consider importing the OpenZeppelin contracts instead of re-implementing/copying them. You can add extra functionalities to the contracts by extending them.

For instance:

File: `ReentrancyGuard.sol` [Line 6-10](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ReentrancyGuard.sol#L6-L10)

```
/**
 * @title ReentrancyGuard
 * @notice This contract protects against reentrancy attacks.
 *         It is adjusted from OpenZeppelin.
 */
```

## Lines are too long

For readability, the maximum suggested line length is 120 characters.

Here are some instances of this issue:

File: `SeaportProxy.sol` [Line 35](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L35)
File: `SeaportProxy.sol` [Line 37](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L37)
