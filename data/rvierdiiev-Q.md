### 1. LooksRareAggregator.setFee doesn’t check if feeRecipient is not 0 address.
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L153-L163
There is no check that `recipient` param is not 0 address in LooksRareAggregator.setFee. 
As result SeaPort proxy will [not transfer]( https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L206) protocol fees. 