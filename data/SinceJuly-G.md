# 01 Parameters in External Function Should Declared as Calldata

## Lines of code 
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenReceiver.sol#L19
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenReceiver.sol#L27-L29
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IGemSwap.sol#L11

## Descritpion
Parameters of functions with visibility external should be declared as calldata, which can save gas.
```solidity
function onERC1155Received( 
    address,
    address,
    uint256,
    uint256,
    bytes memory
) external virtual returns (bytes4) {
    return this.onERC1155Received.selector;
}

function onERC1155BatchReceived(
    address,
    address,
    uint256[] memory,
    uint256[] memory,
    bytes memory
) external virtual returns (bytes4) {
    return this.onERC1155BatchReceived.selector;
}
```
```solidity
interface IGemSwap {
    struct TradeDetails {
        uint256 marketId;
        uint256 value;
        bytes tradeData;
    }

    function batchBuyWithETH(TradeDetails[] memory tradeDetails) external payable;
}
```

## Recommendation 
It is recommended to declare the above mentioned function parameters as calldata.

# 02 Make erc20EnabledLooksRareAggregator immutable

## Lines of Code
LooksRareAggregator.sol:44

## Description
since `erc20EnabledLooksRareAggregator` is designed to be set only once to improve security, it is possible to make it immutable to save gas without compromising on security.<br />The reason `erc20EnabledLooksRareAggregator` must be set after LooksRareAggregator is deployed is that `ERC20EnabledLooksRareAggregator`contract uses `LooksRareAggregator`contract address as an immutable variable. Therefore, `ERC20EnabledLooksRareAggregator`contract must be deployed first to get its address and set it on the deployment of `LooksRareAggregator`contract.<br />However, it is possible to pre-calculate `ERC20EnabledLooksRareAggregator`'s address using CREATE2 in advance, and make both `erc20EnabledLooksRareAggregator` and `aggregator` immutable. In such a way, we are able to remove `setERC20EnabledLooksRareAggregator`function and one slot in storage, and also save one read of `setERC20EnabledLooksRareAggregator` at each `execute()` call.
### Recommendation

1. Pre-compute `ERC20EnabledLooksRareAggregator`'s address using Openzepplin CLI, [https://docs.openzeppelin.com/cli/2.8/deploying-with-create2#create2-from-the-cli](https://docs.openzeppelin.com/cli/2.8/deploying-with-create2#create2-from-the-cli)
2. Make `erc20EnabledLooksRareAggregator` an immutable variable, and remove `setERC20EnabledLooksRareAggregator`function. 
```solidity
contract LooksRareAggregator is
    ILooksRareAggregator,
    TokenRescuer,
    TokenReceiver,
    ReentrancyGuard,
    LowLevelERC20Approve,
    LowLevelERC721Transfer,
    LowLevelERC1155Transfer
{
    address public immutable erc20EnabledLooksRareAggregator;
  
    constructor(address _erc20EnabledLooksRareAggregator) {
    	erc20EnabledLooksRareAggregator = _erc20EnabledLooksRareAggregator;
      emit ERC20EnabledLooksRareAggregatorSet();
    }

    ...
    /* function setERC20EnabledLooksRareAggregator can be removed
    function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external onlyOwner {
        if (erc20EnabledLooksRareAggregator != address(0)) revert AlreadySet();
        erc20EnabledLooksRareAggregator = _erc20EnabledLooksRareAggregator;
        emit ERC20EnabledLooksRareAggregatorSet();
    }
    */
    ...
}
```

3. Deploy `LooksRareAggregator`contract, and set `erc20EnabledLooksRareAggregator`as pre-compute `ERC20EnabledLooksRareAggregator`contract's address on constructor.
4. Deploy `ERC20EnabledLooksRareAggregator`contract and set aggregator's address as deployed address in step 3.
