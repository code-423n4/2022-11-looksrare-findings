# Gas Optimization

In LooksRareAggregator contract the function _returnERC20TokensIfAny is calculating tokenTransfers[i].currency two times (on line number 244 and 245) which is consuming more gas than normal so instead this we can catch its value only one time which can take less gas.

```
function _returnERC20TokensIfAny(TokenTransfer[] calldata tokenTransfers, address recipient) private {
        uint256 tokenTransfersLength = tokenTransfers.length;
        for (uint256 i; i < tokenTransfersLength; ) {
            uint256 balance = IERC20(tokenTransfers[i].currency).balanceOf(address(this));
            if (balance > 0) _executeERC20DirectTransfer(tokenTransfers[i].currency, recipient, balance);

            unchecked {
                ++i;
            }
        }
    }
```

