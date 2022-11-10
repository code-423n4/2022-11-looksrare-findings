Unlocked pragma: Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the pragma (for e.g. by not using ^ in pragma solidity 0.5.10) ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs. (see here: https://swcregistry.io/docs/SWC-103)
example:
https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC1155.sol#L2
https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L2
==========================================================

Multiple Solidity pragma: It is better to use one Solidity compiler version across all contracts instead of different versions with different bugs and security checks. (see here: https://github.com/crytic/slither/wiki/Detector-Documentation#different-pragma-directives-are-used)
example:
https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/ERC20EnabledLooksRareAggregator.sol#L2
https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L2
https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/interfaces/IERC1155.sol#L2
==========================================================

Avoid transfer()/send() as reentrancy mitigations: Although transfer() and send() have been recommended as a security best-practice to prevent reentrancy attacks because they only forward 2300 gas, the gas repricing of opcodes may break deployed contracts. Use call() instead, without hardcoded gas limits along with checks-effects-interactions pattern or reentrancy guards for reentrancy protection. (see here: https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/  and here: https://swcregistry.io/docs/SWC-134)
https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/ERC20EnabledLooksRareAggregator.sol#L28
==========================================================
