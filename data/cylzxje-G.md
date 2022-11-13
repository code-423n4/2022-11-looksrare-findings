### [G-01] Use `storage` instead of memory
contracts/LooksRareAggregator.sol
- https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L227

Recommend:
```sol
FeeData storage feeData = _proxyFeeData[singleTradeData.proxy];
```