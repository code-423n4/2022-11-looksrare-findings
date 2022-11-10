## Don't save gas as expected
This code was designed to not writing to storage from zero to nonzero and instead nonzero to nonzero
But by add `-1` it will have one more step to check underflow because it is `0.8.17`
### Proof of Concept
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenRescuer.sol#L22-L26
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenRescuer.sol#L34-L38
### Recommended Mitigation Steps
Use unchecked and 
`if (withdrawAmount == 0 || withdrawAmount = type(uint256).max)`

## Missing Input Sanity Check
Constructor missing zero check
### Proof of Concept
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L38-L39
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L46-L47
