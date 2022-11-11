When I analysis the TxHash: https://phalcon.blocksec.com/tx/eth/0x298afa2310f81496fb94325150fde0a1d6cb7b9b14c9d83c73a2ac28010a2d89

There was a contract had been called: ***LooksRare: Fee Sharing***

The contract related to FeeSharingSystem.sol

https://etherscan.io/address/0xBcD7254A1D759EFA08eC7c3291B2E85c5dCC12ce?utm_source=immunefi#code


The FeeSharingSystem.sol included two withdraw functions "withdraw" and "withdrawAll".
It seems that there is no validation in function "withdrawAll"

```
function withdrawAll(bool claimRewardToken) external nonReentrant {
        _withdraw(userInfo[msg.sender].shares, claimRewardToken);
}
```

The function withdraw sender shares, it seems that it allows 0 / < 0 numbers.