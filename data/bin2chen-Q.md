1:
LooksRareAggregator#setFee() suggests adding check recipient cannot be address(this), because after #execute() will be the remaining ERC20 or ETH are transferred away (_returnERC20TokensIfAny/_ returnETHIfAny), if it is #setFee() to the current contract, so it will be taken away, the contract lost fee
suggest:
```
    function setFee(
        address proxy,
        uint256 bp,
        address recipient
    ) external onlyOwner {
        if (bp > 10000) revert FeeTooHigh();
+       require(recipient!=address(this),"can't equal address(this)")        
        _proxyFeeData[proxy].bp = bp;
        _proxyFeeData[proxy].recipient = recipient;

        emit FeeUpdated(proxy, bp, recipient);
    }
```