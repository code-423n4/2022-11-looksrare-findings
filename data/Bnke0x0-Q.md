
### [L01] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:

```
2022-11-looksrare/contracts/LooksRareAggregator.sol::122 => erc20EnabledLooksRareAggregator = _erc20EnabledLooksRareAggregator;
2022-11-looksrare/contracts/LooksRareAggregator.sol::227 => FeeData memory feeData = _proxyFeeData[singleTradeData.proxy];
2022-11-looksrare/contracts/OwnableTwoSteps.sol::126 => delay = _delay;
2022-11-looksrare/contracts/proxies/LooksRareProxy.sol::39 => aggregator = _aggregator;
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::47 => aggregator = _aggregator;
```


### [L02] Unused `receive()` function will lock Ether in contract

#### Impact
If the intention is for the Ether to be used, the function should call another function, otherwise, it should revert
#### Findings:
```
2022-11-looksrare/contracts/LooksRareAggregator.sol::220 => receive() external payable {}
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::86 => receive() external payable {}
```



### [L03] Unspecific Compiler Version Pragma

#### Impact
void floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
2022-11-looksrare/contracts/OwnableTwoSteps.sol::2 => pragma solidity ^0.8.14;
2022-11-looksrare/contracts/ReentrancyGuard.sol::2 => pragma solidity ^0.8.14;
2022-11-looksrare/contracts/SignatureChecker.sol::2 => pragma solidity ^0.8.14;
2022-11-looksrare/contracts/interfaces/IERC1155.sol::2 => pragma solidity ^0.8.0;
2022-11-looksrare/contracts/interfaces/IERC20.sol::2 => pragma solidity ^0.8.0;
2022-11-looksrare/contracts/interfaces/IERC721.sol::2 => pragma solidity ^0.8.0;
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol::2 => pragma solidity ^0.8.14;
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Approve.sol::2 => pragma solidity ^0.8.14;
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Transfer.sol::4 => pragma solidity ^0.8.14;
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC721Transfer.sol::2 => pragma solidity ^0.8.14;
2022-11-looksrare/contracts/lowLevelCallers/LowLevelETH.sol::2 => pragma solidity ^0.8.14;
```





#### Non-Critical Issues



### [N01] `require()`/`revert()` statements should have descriptive reason strings


#### Findings:
```
2022-11-looksrare/contracts/LooksRareAggregator.sol::95 => revert(add(32, returnData), returnDataSize)
```



### [N02] constants should be defined rather than using magic numbers


#### Findings:
```
2022-11-looksrare/contracts/LooksRareAggregator.sol::158 => if (bp > 10000) revert FeeTooHigh();
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::147 => uint256 orderFee = (orders[i].price * feeBp) / 10000;
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::207 => uint256 orderFee = (price * feeBp) / 10000;
```



### [N03] Use a more recent version of solidity


#### Findings:
```
2022-11-looksrare/contracts/interfaces/IERC1155.sol::2 => pragma solidity ^0.8.0;
2022-11-looksrare/contracts/interfaces/IERC20.sol::2 => pragma solidity ^0.8.0;
2022-11-looksrare/contracts/interfaces/IERC721.sol::2 => pragma solidity ^0.8.0;
```





### [N04] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields
#### Findings:
```
2022-11-looksrare/contracts/interfaces/IERC1155.sol::15 => event ApprovalForAll(address indexed account, address indexed operator, bool approved);
2022-11-looksrare/contracts/interfaces/IERC20.sol::5 => event Transfer(address indexed from, address indexed to, uint256 value);
2022-11-looksrare/contracts/interfaces/IERC20.sol::7 => event Approval(address indexed owner, address indexed spender, uint256 value);
2022-11-looksrare/contracts/interfaces/IERC721.sol::9 => event ApprovalForAll(address indexed owner, address indexed operator, bool approved);
2022-11-looksrare/contracts/interfaces/ILooksRareAggregator.sol::44 => event FeeUpdated(address proxy, uint256 bp, address recipient);
```





### [N05] Unused file


#### Findings:
```
2022-11-looksrare/contracts/ERC20EnabledLooksRareAggregator.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/LooksRareAggregator.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/OwnableTwoSteps.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/ReentrancyGuard.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/SignatureChecker.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/TokenReceiver.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/TokenRescuer.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/TokenTransferrer.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/interfaces/IERC1155.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/interfaces/IERC20.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/interfaces/IERC20EnabledLooksRareAggregator.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/interfaces/IERC721.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/interfaces/ILooksRareAggregator.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/interfaces/IProxy.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/interfaces/SeaportInterface.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/libraries/OrderEnums.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/libraries/OrderStructs.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/libraries/seaport/ConsiderationEnums.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/libraries/seaport/ConsiderationStructs.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Approve.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Transfer.sol::3 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC721Transfer.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/lowLevelCallers/LowLevelETH.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/proxies/LooksRareProxy.sol::1 => // SPDX-License-Identifier: MIT
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::1 => // SPDX-License-Identifier: MIT
```



### [N06] Numeric values having to do with time should use time units for readability

#### Impact
There are units for seconds, minutes, hours, days, and weeks
#### Findings:
```
2022-11-looksrare/contracts/OwnableTwoSteps.sol::18 => // Delay for the timelock (in seconds)
```



### [N07] NC-library/interface files should use fixed compiler versions, not floating ones


#### Findings:
```
2022-11-looksrare/contracts/OwnableTwoSteps.sol::2 => pragma solidity ^0.8.14;
2022-11-looksrare/contracts/ReentrancyGuard.sol::2 => pragma solidity ^0.8.14;
2022-11-looksrare/contracts/SignatureChecker.sol::2 => pragma solidity ^0.8.14;
2022-11-looksrare/contracts/interfaces/IERC1155.sol::2 => pragma solidity ^0.8.0;
2022-11-looksrare/contracts/interfaces/IERC20.sol::2 => pragma solidity ^0.8.0;
2022-11-looksrare/contracts/interfaces/IERC721.sol::2 => pragma solidity ^0.8.0;
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol::2 => pragma solidity ^0.8.14;
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Approve.sol::2 => pragma solidity ^0.8.14;
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Transfer.sol::4 => pragma solidity ^0.8.14;
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC721Transfer.sol::2 => pragma solidity ^0.8.14;
2022-11-looksrare/contracts/lowLevelCallers/LowLevelETH.sol::2 => pragma solidity ^0.8.14;
```




