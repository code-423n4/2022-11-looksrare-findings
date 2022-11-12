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

Account existence check for low-level calls: Low-level calls call/delegatecall/staticcall return true even if the account called is non-existent (per EVM design). Account existence must be checked prior to calling if needed. (see here: https://github.com/crytic/slither/wiki/Detector-Documentation#low-level-calls )

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L88
==========================================================

DoS with block gas limit: Programming patterns such as looping over arrays of unknown size may lead to DoS when the gas cost of execution exceeds the block gas limit. (see here: https://swcregistry.io/docs/SWC-128 )

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L69
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L243
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/ERC20EnabledLooksRareAggregator.sol#L41
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L64
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L102
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L126
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L145
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L187
==========================================================

Unindexed event parameters: Parameters of certain events are expected to be indexed (e.g. ERC20 Transfer/Approval events) so that they’re included in the block’s bloom filter for faster access. Failure to do so might confuse off-chain tooling looking for such indexed events. (see here:  https://github.com/crytic/slither/wiki/Detector-Documentation#unindexed-erc20-event-oarameters )
Events Indexing
Events should use the maximum amount of indexed fields: up to three parameters. This makes it easier to filter for specific values in front-ends.

Proof of Concept

Recommended Mitigation Steps Add indexed fields to these events so that they have the maximum number of indexed fields possible.

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC1155.sol#L15-L17
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/ILooksRareAggregator.sol#L44-L58
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IOwnableTwoSteps.sol#L25-L27
==========================================================

Missing zero address validation: Setters of address type parameters should include a zero-address check otherwise contract functionality may become inaccessible or tokens burnt forever. (see here: https://github.com/crytic/slither/wiki/Detector-Documentation#missing-zero-address-validation )

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/ERC20EnabledLooksRareAggregator.sol#L21
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/ERC20EnabledLooksRareAggregator.sol#L31
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/ERC20EnabledLooksRareAggregator.sol#L39
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L54
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L143
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L172-L173
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L184
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L196-L197
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L241
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L98
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L110
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenRescuer.sol#L22
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenRescuer.sol#L34
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenTransferrer.sol#L16-L17
==========================================================
