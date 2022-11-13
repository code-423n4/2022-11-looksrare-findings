# LooksRareAggregator#setERC20EnabledLooksRareAggregator allows to emit nonsense event if zero is pass as new address
If ```_erc20EnabledLooksRareAggregator = address(0)```, ```setERC20EnabledLooksRareAggregator``` won't revert, and it will emit nonsense event ```ERC20EnabledLooksRareAggregatorSet```. This function should check that ```_erc20EnabledLooksRareAggregator != address(0)```

# LooksRareAggregator#addFunction allows to emit nonsense event if already added proxy and selector is sent
If proxy function was already added, then ```addFunction``` will emit a nonsense event. The function should check that ```_proxyFunctionSelectors[proxy][selector]``` is ```false```.

# LooksRareAggregator#removeFunction allows to emit nonsense event if not added proxy and selector is sent
If proxy function was not already added, then ```removeFunction``` will emit a nonsense event. The function should check that ```_proxyFunctionSelectors[proxy][selector]``` is ```true```.

# LooksRareAggregator#setFee allows to emit nonsense event if fees are not really changed
If current values of ```_proxyFeeData[proxy]``` are sent, then the function will emit a nonsense event. This function should check that ```_proxyFeeData[proxy].bp != bp && _proxyFeeData[proxy].recipient != recipient;```

# ERC20EnabledLooksRareAggregator#constructor allows to set address zero to aggregator
It should be checked that ```_aggregator != address(0)```, if ```_aggregator == address(0)``` it will be needed to redeploy.

# LooksRareProxy#constructor allows to set address zero to aggregator and marketplace
It should be checked that ```_aggregator != address(0)``` and ```_marketplace != address(0)```, if ```_aggregator == address(0)``` or ```_marketplace == address(0)``` it will be needed to redeploy.

# SeaportProxy#constructor allows to set address zero to aggregator and marketplace
It should be checked that ```_aggregator != address(0)``` and ```_marketplace != address(0)```, if ```_aggregator == address(0)``` or ```_marketplace == address(0)``` it will be needed to redeploy.

# _executeERC721TransferFrom, _executeERC1155SafeTransferFrom, _executeERC1155SafeBatchTransferFrom do not check contract length
The problem has similarities with one reported in [OlympusDao contest](https://code4rena.com/reports/2022-08-olympus/#m-02-solmate-safetransfer-and-safetransferfrom-does-not-check-the-codesize-of-the-token-address-which-may-lead-to-fund-loss). The problem is the ERC721 and ERC1155 contract length lack of check. These function in the whole interaction of the present contract does not present major issues, however if they are intended to be used by other contracts it may well present several high issues.