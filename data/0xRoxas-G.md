# Gas Report
## Optimizations found [1]

### [G-01] Use Nested If Instead [ⓘ](https://github.com/devanshbatham/Solidity-Gas-Optimization-Tips#6--use-nested-if-and-avoid-multiple-check-combinations)

#### Findings:

*/contracts/SignatureChecker.sol*
Line(s): [48](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L48)
```solidity
48:	if (v != 27 && v != 28) revert BadSignatureV(v);
```