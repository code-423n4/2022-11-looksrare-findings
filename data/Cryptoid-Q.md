First Issue I found is in this contract: https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20.sol

In  line 19 of this contract there is a transferFrom instead of a safeTransferFrom.

Second Issue I found is found in this contract: https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC721.sol

In line 28 of this contract there is a transferFrom instead of a safeTransferFrom.
