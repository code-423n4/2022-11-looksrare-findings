**********L-01********** Code uses `ecrecover` which is susceptible to signature malleability

- [https://github.com/code-423n4/2022-11-looksrare/blob/e42ac05b3b740292422a725e9b57687e62d32c67/contracts/SignatureChecker.sol#L60](https://github.com/code-423n4/2022-11-looksrare/blob/e42ac05b3b740292422a725e9b57687e62d32c67/contracts/SignatureChecker.sol#L60)

It is a best practice to use OpenZeppelin’s ECDSA library for recovering the signer of a signature, because of the inherent signature malleability vulnerability of `ecrecover`

**********L-02********** Constructors are missing non-zero address checks

- [https://github.com/code-423n4/2022-11-looksrare/blob/e42ac05b3b740292422a725e9b57687e62d32c67/contracts/proxies/SeaportProxy.sol#L45](https://github.com/code-423n4/2022-11-looksrare/blob/e42ac05b3b740292422a725e9b57687e62d32c67/contracts/proxies/SeaportProxy.sol#L45)

Add non-zero address checks for all `address` type arguments in all constructors for safety

************NC-01************ Use latest version of Solidity without a floating pragma

- [https://github.com/code-423n4/2022-11-looksrare/blob/e42ac05b3b740292422a725e9b57687e62d32c67/contracts/OwnableTwoSteps.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/e42ac05b3b740292422a725e9b57687e62d32c67/contracts/OwnableTwoSteps.sol#L2)

Latest Solidity version is 0.8.17, use it to get latest compiler features and optimisations and do not use a floating pragma as it is a bad practice.