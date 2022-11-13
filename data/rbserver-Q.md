## [L-01] CALLING `rescueERC721` FUNCTION CAN POSSIBLY LOCK THE TRANSFERRED ERC721 TOKEN
Calling the `rescueERC721` function further calls the `_executeERC721TransferFrom` function. Calling the `_executeERC721TransferFrom` function then executes `(bool status, ) = collection.call(abi.encodeWithSelector(IERC721.transferFrom.selector, from, to, tokenId))`. Since the `IERC721.transferFrom` function selector is used, if the address corresponding to the `to` input is a contract that does not support ERC721 protocol, then it is possible that the ERC721 token is transferred to an address that cannot interact with the corresponding ERC721 contract. As a result, the transferred ERC721 is locked and cannot be retrieved.

For reference, [OpenZeppelin's documentation for `transferFrom`](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721#IERC721-transferFrom-address-address-uint256-) states: "
Note that the caller is responsible to confirm that the recipient is capable of receiving ERC721 or else they may be permanently lost." Please consider updating the `_executeERC721TransferFrom` function to use the corresponding `IERC721.safeTransferFrom` function selector instead of the `IERC721.transferFrom` function selector.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L195-L201
```solidity
    function rescueERC721(
        address collection,
        address to,
        uint256 tokenId
    ) external onlyOwner {
        _executeERC721TransferFrom(collection, address(this), to, tokenId);
    }
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L21-L29
```solidity
    function _executeERC721TransferFrom(
        address collection,
        address from,
        address to,
        uint256 tokenId
    ) internal {
        (bool status, ) = collection.call(abi.encodeWithSelector(IERC721.transferFrom.selector, from, to, tokenId));
        if (!status) revert ERC721TransferFromFail();
    }
```

## [L-02] LACK OF GOVERNANCE AND VOTING MECHANISM FOR CALLING `rescueERC721`, `rescueERC1155`, `rescueETH`, and `rescueERC20` FUNCTIONS
The `LooksRareAggregator` contract's owner has the privilege to call the `rescueERC721`, `rescueERC1155`, `rescueETH`, and `rescueERC20` functions to transfer the corresponding trapped assets to the specified addresses. Looking at https://docs.looksrare.org/developers/deployed-contract-addresses, it appears to be that this protocol does not have a governance contract that lets the community to vote. This means that the `LooksRareAggregator` contract's owner cannot be such governance contract currently, and there is no guarantee that the `LooksRareAggregator` contract's current owner will not become compromised or malicious in the future. When the `LooksRareAggregator` contract's owner becomes compromised or malicious, this owner can call the `rescueERC721`, `rescueERC1155`, `rescueETH`, and `rescueERC20` contracts to immediately withdraw the corresponding trapped assets so these assets would be lost forever. Please consider setting up a governance contract and transferring the ownership of the `LooksRareAggregator` contract to this governance contract so the community can vote on how these `rescueERC721`, `rescueERC1155`, `rescueETH`, and `rescueERC20` functions can be called.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L195-L201
```solidity
    function rescueERC721(
        address collection,
        address to,
        uint256 tokenId
    ) external onlyOwner {
        _executeERC721TransferFrom(collection, address(this), to, tokenId);
    }
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L211-L218
```solidity
    function rescueERC1155(
        address collection,
        address to,
        uint256[] calldata tokenIds,
        uint256[] calldata amounts
    ) external onlyOwner {
        _executeERC1155SafeBatchTransferFrom(collection, address(this), to, tokenIds, amounts);
    }
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenRescuer.sol#L22-L26
```solidity
    function rescueETH(address to) external onlyOwner {
        uint256 withdrawAmount = address(this).balance - 1;
        if (withdrawAmount == 0) revert InsufficientAmount();
        _transferETH(to, withdrawAmount);
    }
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenRescuer.sol#L34-L38
```solidity
    function rescueERC20(address currency, address to) external onlyOwner {
        uint256 withdrawAmount = IERC20(currency).balanceOf(address(this)) - 1;
        if (withdrawAmount == 0) revert InsufficientAmount();
        _executeERC20DirectTransfer(currency, to, withdrawAmount);
    }
```

## [L-03] MISSING `address(0)` CHECKS FOR CRITICAL ADDRESS INPUTS
To prevent unintended behaviors, critical address inputs should be checked against `address(0)`.

Please consider checking `_aggregator` in the following constructor.
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L21-L23
```solidity
    constructor(address _aggregator) {
        aggregator = ILooksRareAggregator(_aggregator);
    }
```

Please consider checking `_marketplace` and `_aggregator` in the following constructor.
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L37-L40
```solidity
    constructor(address _marketplace, address _aggregator) {
        marketplace = ILooksRareExchange(_marketplace);
        aggregator = _aggregator;
    }
```

Please consider checking `_marketplace` and `_aggregator` in the following constructor.
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L45-L48
```solidity
    constructor(address _marketplace, address _aggregator) {
        marketplace = SeaportInterface(_marketplace);
        aggregator = _aggregator;
    }
```

## [N-01] REPEATED AND REDUNDANT CODE CAN BE REMOVED
`TokenRescuer` is imported twice in `LooksRareAggregator.sol`. Please remove one of these `import` code lines.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L11-L17
```solidity
import {TokenRescuer} from "./TokenRescuer.sol";
...
import {TokenRescuer} from "./TokenRescuer.sol";
```

## [N-02] CONSTANTS CAN BE USED INSTEAD OF MAGIC NUMBERS
To improve readability and maintainability, constants can be used instead of magic numbers. Please note that the following magic numbers are not flagged in https://gist.github.com/Picodes/0554505ff357a2da3bca5fbb839ee7da.

Please consider replacing the following magic numbers, such as `2`, with constants.
```solidity
contracts\LooksRareAggregator.sol
  158: if (bp > 10000) revert FeeTooHigh();    

contracts\proxies\SeaportProxy.sol
  147: uint256 orderFee = (orders[i].price * feeBp) / 10000;   
  207: uint256 orderFee = (price * feeBp) / 10000; 
  246: offer[0].itemType = ItemType(uint8(order.collectionType) + 2);  
```

## [N-03] INCOMPLETE NATSPEC COMMENT
NatSpec comments provide rich code documentation. The @param comment is missing for the following `_returnETHIfAny` function. Please consider completing the NatSpec comment for it.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol#L43-L49
```solidity
    function _returnETHIfAny(address recipient) internal {
        assembly {
            if gt(selfbalance(), 0) {
                let status := call(gas(), recipient, selfbalance(), 0, 0, 0, 0)
            }
        }
    }
```

## [N-04] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. NatSpec comments are missing for the following functions. Please consider adding them.

```solidity
contracts\ERC20EnabledLooksRareAggregator.sol
  39: function _pullERC20Tokens(TokenTransfer[] calldata tokenTransfers, address source) private {    

contracts\LooksRareAggregator.sol
  222: function _encodeCalldataAndValidateFeeBp(   
  241: function _returnERC20TokensIfAny(TokenTransfer[] calldata tokenTransfers, address recipient) private {   

contracts\TokenTransferrer.sol
  14: function _transferTokenToRecipient( 

contracts\proxies\LooksRareProxy.sol
  107: function _executeSingleOrder(   

contracts\proxies\SeaportProxy.sol
  88: function _executeAtomicOrders(  
  136: function _handleFees(   
  166: function _transferFee(   
  178: function _executeNonAtomicOrders(   
  227: function _populateParameters(BasicOrder calldata order, OrderExtraData memory orderExtraData)   
```