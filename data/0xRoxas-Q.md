# QA Report
## Found [3]

### [Q-01] Pragma is not Forced (Floating Pragma)

https://swcregistry.io/docs/SWC-103

#### Findings:

*/contracts/ReentrancyGuard.sol*
Line(s): [2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ReentrancyGuard.sol#L2)
```solidity
2:	pragma solidity ^0.8.14;
```

*/contracts/OwnableTwoSteps.sol*
Line(s): [2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L2)
```solidity
2:	pragma solidity ^0.8.14;
```

*/contracts/SignatureChecker.sol*
Line(s): [2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L2)
```solidity
2:	pragma solidity ^0.8.14;
```

*/contracts/lowLevelCallers/LowLevelERC20Approve.sol*
Line(s): [2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L2)
```solidity
2:	pragma solidity ^0.8.14;
```

*/contracts/lowLevelCallers/LowLevelERC20Transfer.sol*
Line(s): [4](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L4)
```solidity
4:	pragma solidity ^0.8.14;
```

*/contracts/lowLevelCallers/LowLevelERC721Transfer.sol*
Line(s): [2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L2)
```solidity
2:	pragma solidity ^0.8.14;
```

*/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol*
Line(s): [2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L2)
```solidity
2:	pragma solidity ^0.8.14;
```

*/contracts/lowLevelCallers/LowLevelETH.sol*
Line(s): [2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol#L2)
```solidity
2:	pragma solidity ^0.8.14;
```

*/contracts/interfaces/IERC20.sol*
Line(s): [2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20.sol#L2)
```solidity
2:	pragma solidity ^0.8.0;
```

*/contracts/interfaces/IERC721.sol*
Line(s): [2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC721.sol#L2)
```solidity
2:	pragma solidity ^0.8.0;
```

### [Q-02] Underscore Notation Improves Readability

Solidity allows for the use of _ between every 3 digits of large numbers.

#### Findings:

*/contracts/proxies/SeaportProxy.sol*
Line(s): [147](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L147), [207](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L207)
```solidity
147:	uint256 orderFee = (orders[i].price * feeBp) / 10000;
207:	uint256 orderFee = (price * feeBp) / 10000;
```

*/contracts/LooksRareAggregator.sol*
Line(s): [158](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L158)
```solidity
158:	if (bp > 10000) revert FeeTooHigh();
```

### [NC-03] Magic Number Used

#### Findings [1]:

A magic number `10000` is used. Consider making a variable in `10000`'s place.

*/contracts/LooksRareAggregator.sol*
Line(s): [158](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L158)
```solidity
158:	if (bp > 10000) revert FeeTooHigh();
```