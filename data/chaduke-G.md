G01: https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L241
Assume ``tokensTransfers`` is sorted based on concurrency, then multiple transfers of the same currency can be combined into one transaction to save gas.

G02: https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/ERC20EnabledLooksRareAggregator.sol#L39-L53
Assume ``tokensTransfers`` is sorted based on concurrency, then multiple transfers of the same currency can be combined into one transaction to save gas.

G03: QA9: https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L44
``erc20EnabledLooksRareAggregator`` should be declared as immutable and private to save gas
```
address private immutable erc20EnabledLooksRareAggregator;
```

G04: https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L72
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L62
These two checks can be performed in the calling contract ``LooksRareAggregator``, right before the delegatecall at L88 to save gas.

