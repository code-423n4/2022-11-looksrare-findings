

## L-01 Unused receive() function will lock Ether in contract

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert.

Instances (2):

LooksRareAggregator.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L220

SeaportProxy.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L86


