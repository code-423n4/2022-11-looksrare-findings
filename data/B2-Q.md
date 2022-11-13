
## Missing checks for `address(0x0)` when assigning values to `address` state variables

Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly.

```
        aggregator = ILooksRareAggregator(_aggregator);
```

>File: contracts/ERC20EnabledLooksRareAggregator.sol [(line 22)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L22)
```
        marketplace = SeaportInterface(_marketplace);
        aggregator = _aggregator;
```

>File: contracts/proxies/SeaportProxy.sol [(line 46-47)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L46-L47)



## Inconsistent solidity pragma

The source files have different solidity compiler ranges referenced. This leads to potential security flaws between deployed contracts depending on the compiler version chosen for any particular file. It also greatly increases the cost of maintenance as different compiler versions have different semantics and behavior.

Recommend fixing a definite compiler range that is consistent between contracts and upgrade any affected contracts to conform to the specified compiler.

file [( IERC20EnabledLooksRareAggregator.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20EnabledLooksRareAggregator.sol), [( ILooksRareAggregator.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol), [( IProxy.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IProxy.sol),  [( SeaportInterface.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/SeaportInterface.sol), [( ConsiderationEnums.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/seaport/ConsiderationEnums.sol), [( ConsiderationStructs.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/seaport/ConsiderationStructs.sol), [( OrderEnums.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/OrderEnums.sol), [( OrderStructs.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/OrderStructs.sol), [( SeaportProxy.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol),  [( ERC20EnabledLooksRareAggregator.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol), [( LooksRareAggregator.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol), [( TokenReceiver.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenReceiver.sol),  [( TokenTransferrer.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenTransferrer.sol), [( TokenRescuer.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenRescuer.sol)

---> (pragma solidity 0.8.17;)

file [( LowLevelETH.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol), [( LowLevelERC1155Transfer.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol), [( LowLevelERC721Transfer.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol), [( LowLevelERC20Transfer.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol), [( LowLevelERC20Approve.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol), [( SignatureChecker.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L60
), [( OwnableTwoSteps.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L114)

---> (pragma solidity ^0.8.14;)


file [( IERC20.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20.sol
), [( IERC721.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC721.sol
), [( IERC1155.sol)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC1155.sol)

---> (pragma solidity ^0.8.0;)



## Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

```
	contract SeaportProxy is IProxy, TokenRescuer {
```
>File: contracts/proxies/SeaportProxy.sol [(line 19)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L19)
```
	contract ERC20EnabledLooksRareAggregator is IERC20EnabledLooksRareAggregator, LowLevelERC20Transfer {
```
>File: contracts/ERC20EnabledLooksRareAggregator.sol [(line 15)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L15)

```
Other instances of this issue are :
```
* https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L25-L32
* https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenRescuer.sol#L14



## Use of `block.timestamp`

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers, locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
        if (block.timestamp < earliestOwnershipRenouncementTime) revert RenouncementTooEarly();
```
>File: contracts/OwnableTwoSteps.sol [(line 70)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L70)

```
        earliestOwnershipRenouncementTime = block.timestamp + delay;
```
>File: contracts/OwnableTwoSteps.sol [(line 114)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L114)



## Unused `receive()` function will lock Ether in contract
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

```
    receive() external payable {}
```

>File: /contracts/proxies/SeaportProxy.sol [(line 86)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L86)
```
    receive() external payable {}
```

>File: contracts/LooksRareAggregator.sol [(line 220)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L220)




## Event is missing `indexed` fields

Each event should use three indexed fields if there are three or more fields.

```
    event FeeUpdated(address proxy, uint256 bp, address recipient);
```
>File: contracts/interfaces/ILooksRareAggregator.sol [(line 44)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol#L44)




## Use of `ecrecover()`
```
        signer = ecrecover(hash, v, r, s);
```
>File: contracts/SignatureChecker.sol [(line 60)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L60)



## Missing initializer modifier on constructor

OpenZeppelin recommends that the initializer modifier be applied to constructors in order to avoid potential griefs, social engineering, or exploits. Ensure that the modifier is applied to the implementation contract. If the default constructor is currently being used, it should be changed to be an explicit one with the modifier applied.

```
	contract SeaportProxy is IProxy, TokenRescuer {
```
>File: contracts/proxies/SeaportProxy.sol [(line 19)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L19)
```
	contract ERC20EnabledLooksRareAggregator is IERC20EnabledLooksRareAggregator, LowLevelERC20Transfer {
```
>File: contracts/ERC20EnabledLooksRareAggregator.sol [(line 15)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L15)


## Set `garbage` value in `mapping` for deleting that

If there is a mapping data structure present inside struct, then deleting the struct doesn't delete the mapping. Instead one should use lock to lock that data structure from further use.


```
        delete _proxyFunctionSelectors[proxy][selector];
```
>File:contracts/LooksRareAggregator.sol [(line 144)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L144)



## `NatSpec` is incomplete

```
    /// @audit Missing: '@return`
    /**
     * @notice Recover the signer of a signature (for EOA)
     * @param hash Hash of the signed message
     * @param signature Bytes containing the signature (64 or 65 bytes)
     */
    function _recoverEOASigner(bytes32 hash, bytes memory signature) internal pure returns (address signer) {

```
* File: contracts/SignatureChecker.sol [(line 51-56)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L51-L56)

```
    /// @audit Missing: '@return`&`@param`
    /**
     * @inheritdoc IERC20EnabledLooksRareAggregator
     */
    function execute(

```
* File: contracts/ERC20EnabledLooksRareAggregator.sol [(line 25-28)](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L25-L28)

```
Other instances of this issue are:
```

* https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol#L40-L43
/// @audit Missing: `@param`
