
## [G-1] Use cache comparison at local variable instead of recalculating **tokenTransfers[i].currency**

In [this](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L244) code at lines [244](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L244) and [245](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L245). It's recalculating value of **tokenTransfers[i].currency** inside loop . which will consume more gas. Instead of recalculating we can store it in local variable and reuse it.  check below sample code where I have used temp as cached local variable. 

```
//Vulnerable code
 for (uint256 i; i < tokenTransfersLength; ) {
            uint256 balance = IERC20(tokenTransfers[i].currency).balanceOf(address(this));
            if (balance > 0) _executeERC20DirectTransfer(tokenTransfers[i].currency, recipient, balance);

            unchecked {
                ++i;
            }
        }

//optimised code
 for (uint256 i; i < tokenTransfersLength; ) {
            uint256 temp = tokenTransfers[i].currency;
            uint256 balance = IERC20(temp).balanceOf(address(this));
            if (balance > 0) _executeERC20DirectTransfer(temp, recipient, balance);

            unchecked {
                ++i;
            }
        }
```