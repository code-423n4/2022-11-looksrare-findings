## If someone accidentally sends ERC20 / ETH to the LooksRareAggregator contract, the next user using the aggregator will be given these token

https://github.com/code-423n4/2022-11-looksrare/blob/f4c90ca149f4aeeac125605a56166297b717201a/contracts/LooksRareAggregator.sol#L108-L109

```solidity
        if (tokenTransfersLength > 0) _returnERC20TokensIfAny(tokenTransfers, originator);
        _returnETHIfAny(originator);
```

These two lines will give the accidentally sent tokens and ETH to the next originator for free!

## LooksRareAggregator may be used to sell NFTs / mass accept offers instead of buying them in the future. Expose a risk for a hacker to steal approved NFTs in case the governance key got compromised

If LooksRareAggregator is used to sell NFTs / mass accept offers, ERC721 or ERC1155 must be approved by the LooksRareAggregator contract.

Hackers may hack into the governance private key and whitelist a malicious contract to perform `ERC721.transferFrom(victim, attacker, tokenId)` on the delegated call

https://github.com/code-423n4/2022-11-looksrare/blob/f4c90ca149f4aeeac125605a56166297b717201a/contracts/LooksRareAggregator.sol#L88

```solidity
(bool success, bytes memory returnData) = singleTradeData.proxy.delegatecall(proxyCalldata);
```