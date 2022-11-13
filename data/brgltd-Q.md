# [01] Lack of check if recipient is capable of receiving NFT for ERC721 transfers

## Impact

Sending the NFT for an address not able to receive NFT will cause loos of funds - the asset will be lost.

## Proof of Concept

Usafe `transferFrom()` in `TokenTransferrer._tranferTokenToRecipient()` when collection type is erc721.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenTransferrer.sol#L22

## Recommended Mitigation Steps

Replace `transferFrom` with `safeTransferFrom` for ERC721 collection type transfers.

# [02] Lack of check for contract existence for ERC20 transfers

## Impact

Solidity's `.call` won't check for contract existance. [Documentation](https://docs.soliditylang.org/en/develop/control-structures.html#error-handling-assert-require-revert-and-exceptions) "The low-level functions call, delegatecall and staticcall return true as their first return value if the account called is non-existent, as part of the design of the EVM. Account existence must be checked prior to calling if needed."

Calling `_executeERC20TransferFrom()` and `_executeERC20DirectTransfer()`, for a token contract that gets destroyed will return success and will be considered a silent failure.

## Proof of Concept

Usage of low level call for erc20 transfer helpers.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L24-L38

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L46-L57

## Recommended Mitigation Steps

Consider adding a check for contract existance on `_executeERC20TransferFrom()` and `_executeERC20DirectTransfer()`. E.g. `if (currency.code.length == 0) revert CustomError()`

# [03] Lack of revert if transaction is not atomic an all items violate the max fee basis point

## Proof of Concept

It's possible to craft a `TradeData` where the transaction is not atomic, but all items result in `maxFeeBpViolated` equal to true. In this case,`delegatecall` won't be executed for any item. 

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L51-L112

## Impact

This can cause slient failures. Also, reverting in case `delegatecall` is not called for any item will ensure no more gas is consumed (`_returnERC20TokensIfAny()` and `_returnETHIfAny()` don't need to be executed.)

## Recommended Mitigation Steps

One solution can be to keep track if the transaction is not atomic and if at least item successfully executed `deletegatecall()`. E.g. is it's not atomic and all items fall through the `continue` statement on [L85](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L85), the transaction can revert after the loop.

# [04] Lack of check if eth transfer is successful

The eth assembly transfer calls don't validate the status. Consider adding an validation statement to prevent silent failures of low level eth transfers. E.g. `if iszero(status) { revert(0, 0) }`.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol#L46

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol#L57

# [05] Avoid floating pragma for non library contracts

The following contracts are not declared as library/abstract and are floating the pragma version.

LowLevelERC20Approve.sol,
LowLevelERC20Transfer.sol,
LowLevelERC721Transfer.sol,
LowLevelERC1155Transfer.sol,
LowLevelETH.sol

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or a package.

# [06] Missing address(0) validation

If a variable gets configured with address zero, failure to immediately reset the value can result in unexpected behavior for the project.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L21-L23

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L37-L40

# [07] Check if `newPotentialOwner` is different than the current owner 

Add a check ensuring that the `newPotentialOwner` argument does not equal the existing `owner`. Otherwise, updating with the same owner will unecessarily trigger events.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L98-L105

# [08] Usage of return named variables

Some functions return named variables, others return explicit values.

Following function return explicit value.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L184

Following function return return a named variable.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L226

Consider adopting the same approach throughout the codebase to improve the explicitness and readability of the code.

# [09] Empty receive

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert. Alternatively, consider documenting the need for the empty receive function.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L220

# [10] Missing natspec

Consider adding natspec on all functions to improve documentation.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L39

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L107

