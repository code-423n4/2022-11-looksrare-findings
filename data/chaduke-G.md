G01: https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L241
Assume ``tokensTransfers`` is sorted based on concurrency, then multiple transfers of the same currency can be combined into one transaction to save gas.

G02: 