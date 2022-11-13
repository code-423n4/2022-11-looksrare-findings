## Q-1 
# `SetFee` does not validate received address. 

If receipt is equals to `address(0)` it could cause lost of fees or even revert transaction during execute.

LooksRareAgregator encode calldata information using `_proxyFeeData[proxy].recipient`.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L237

After that a a `delegateCall` is used to trade NFTs. In this trade we could face lost of fees or even reverts due to missconfiguration.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L88

Suggestion: Check receipt address when setting in `setFee()` method:

```
    function setFee(
        address proxy,
        uint256 bp,
        address recipient
    ) external onlyOwner {
        if (bp > 10000) revert FeeTooHigh();
        //Suggestion
        if (recipient == address(0) && bp != 0) revert ZeroAddress();
        _proxyFeeData[proxy].bp = bp;
        _proxyFeeData[proxy].recipient = recipient;

        emit FeeUpdated(proxy, bp, recipient);
    }


```