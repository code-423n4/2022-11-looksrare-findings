# QA Report for LooksRare Aggregator contest

## Overview
During the audit, 1 low issue was found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | Missing check for zero address | Low | 5

## Low Risk Findings (1)
### L-1. Missing check for zero address
##### Description
If address(0x0) is set it may cause the contract to revert or work wrong.

##### Instances
- https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L38
- https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L39
- https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L46
- https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L47
- https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L22

##### Recommendation
Add checks.