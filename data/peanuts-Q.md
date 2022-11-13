## Table of Contents 

- [L-01] Use OpenZeppelin's ECDSA contract instead of calling ecrecover() directly.
- [N-01] Constants should be defined rather than using magic numbers
- [N-02] Event is missing indexed fields


### [L-01] Use OpenZeppelin's ECDSA contract instead of calling ecrecover() directly.

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/SignatureChecker.sol#L60

### [N-01] Constants should be defined rather than using magic numbers

eg MAX_BPS = 10000

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L158

### [N-02] Event is missing indexed fields

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/ILooksRareAggregator.sol#L44