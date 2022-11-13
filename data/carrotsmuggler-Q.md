# 1. LooksRareAggregator fee can be set to 10000bp
LooksRareAggregator only checks if the set fee bp <= 10000. This allows instances of 10000bp fees, or 100% fees.

# 2. ERC20EnabledAggregator doesnt have recovery option
LooksRareAggregator has token recovery options for erc20, erc721 and erc1155. However ERC20EnabledAggregator does not have recovery options for any of them. It is unlikely ERC20EnabledAggregator will ever come across erc721 or erc1155, but it is quite likely some user might send erc20 to the contract by mistake which will become unrecoverable.

# 3. WETH returns for ETH purchases wont be returned
If protocol returns WETH on ETH purchases, they wont be returned to the user
