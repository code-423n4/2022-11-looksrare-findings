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

ERC20 approve() race condition: Use safeIncreaseAllowance() and safeDecreaseAllowance() from OpenZeppelin’s SafeERC20 implementation to prevent race conditions from manipulating the allowance amounts. (see here: https://swcregistry.io/docs/SWC-114)
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L25
==========================================================
Signature malleability: The ecrecover function is susceptible to signature malleability which could lead to replay attacks. Consider using OpenZeppelin’s ECDSA library: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol . (see here: https://swcregistry.io/docs/SWC-117, here: https://swcregistry.io/docs/SWC-121 and here: https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57)

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/SignatureChecker.sol#L60
==========================================================

ERC20 transfer() does not return boolean: Contracts compiled with solc >= 0.4.22 interacting with such functions will revert. Use OpenZeppelin’s SafeERC20 wrappers. (see here: https://github.com/crytic/slither/wiki/Detector-Documentation#incorrect-erc20-interface  and here: https://medium.com/coinmonks/missing-return-value-bug-at-least-130-tokens-affected-d67bf08521ca )

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L51
==========================================================

fallback vs receive(): Check that all precautions and subtleties of fallback/receive functions related to visibility, state mutability and Ether transfers have been considered.  (see here: https://docs.soliditylang.org/en/latest/contracts.html#fallback-function and here: https://docs.soliditylang.org/en/latest/contracts.html#receive-ether-function )

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L220
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L86
==========================================================
