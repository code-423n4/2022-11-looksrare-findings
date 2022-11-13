### No. 1
In the `LooksRareAggregator` contract, the function `supportsProxyFunction` returns a Boolean value indicating whether a given selector is supported or not. This value is taken from storage without any prior validation. At the same time, any uninitialized storage in Solidity contains the default value zero/false. Querying the function `supportsProxyFunction` for an unknown selector (and unregistered `proxy` address) will return false, thereby misleading the user to believe that the selector is used within the `proxy` and is not supported, while in reality the `proxy` is not registered.
Consider validating the existence of the selector by requiring that the `proxy` address of the selector is registered.

### No. 2
When initiating ownership transfer, it should be checked that `newPotentialOwner` has different address than the current owner. Otherwise, the events emitted `InitiateOwnershipTransfer` and `NewOwner` will be misleading.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L98