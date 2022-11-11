# [L-01] Lack of input validation: LooksRareProxy.sol#rescueERC1155 does not verify the tokenIds  and amounts array length.

### Line Of Code

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L214

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L215

### Vulnerability detail and recommended fix

The function LooksRareProxy.sol#rescueERC1155 does not verify if the length of tokenIds and amounts array length is equal.

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

We recommend the project add the verifcation to make sure length of tokenIds == length of amounts to avoid invalid input and failure to resuces the desired ERC1155 token.

```solidity
require(tokenIds.length == amounts.length, "invalid token length");
```


# [L-02] Lack of timelock for LooksRareProxy.sol#setFee and LooksRareProxy.sol#removeFunction

### Line Of Code

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L153

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L143

### Vulnerability detail and recommended fix

Not all the admin function in LooksRareAggregator deserves a timelock, but I think LooksRareProxy.sol#setFee and LooksRareProxy.sol#removeFunction
are the two function are needs the timelock to make sure the user is aware of this changes and be prepared in advance because the fee change or a deprecated function can definitely affect the traders that use the aggregator to trade NFT.

We recommend adding timelock, can be 3 days, can be 7 days, can be a time period determined by DAO governance, to make sure the change does not take effect immediately!

```solidity
/**
 * @notice Disable calling the specified proxy's trade function
 * @dev Must be called by the current owner
 * @param proxy The marketplace proxy's address
 * @param selector The marketplace proxy's function selector
 */
function removeFunction(address proxy, bytes4 selector) external onlyOwner {
	delete _proxyFunctionSelectors[proxy][selector];
	emit FunctionRemoved(proxy, selector);
}

/**
 * @param proxy Proxy to apply the fee to
 * @param bp Fee basis point
 * @param recipient Fee recipient
 */
function setFee(
	address proxy,
	uint256 bp,
	address recipient
) external onlyOwner {
	if (bp > 10000) revert FeeTooHigh();
	_proxyFeeData[proxy].bp = bp;
	_proxyFeeData[proxy].recipient = recipient;

	emit FeeUpdated(proxy, bp, recipient);
}
```



# [L-03] Lack of input validation in LooksRareProxy.sol#execute when constructing the LooksRare Order.

### Line Of Code

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L80

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L76

### Vulnerability detail and recommended fix

In the LooksRareProxy.sol, we eventually call

```solidity
marketplace.matchAskWithTakerBidUsingETHAndWETH{value: takerBid.price}(takerBid, makerAsk);
```

in LooksRare contract, and if we look into the looksRare contract:

```solidity
function matchAskWithTakerBidUsingETHAndWETH(
	OrderTypes.TakerOrder calldata takerBid,
	OrderTypes.MakerOrder calldata makerAsk
) external payable override nonReentrant {
	require((makerAsk.isOrderAsk) && (!takerBid.isOrderAsk), "Order: Wrong sides");
	require(makerAsk.currency == WETH, "Order: Currency must be WETH");
	require(msg.sender == takerBid.taker, "Order: Taker must be the sender");
```

as we can see, makerAsk.currency == WETH is essential, however, when the order is constructed. maker has passed in any type of the currency.

```solidity
makerAsk.currency = order.currency;
```

but no matter what type of the currency the user passed, the currency needs to be WETH, otherwise the function matchAskWithTakerBidUsingETHandWETH revert.

So we recommend just hardcode the currency as WETH when constructing the order, so we can change from

```solidity
makerAsk.currency = order.currency;
```

to

```solidity
makerAsk.currency = address(WETH);
```

Another part of the order construction is

```solidity
makerAsk.tokenId = order.tokenIds[0];
makerAsk.price = orderExtraData.makerAskPrice;
makerAsk.amount = order.amounts[0];
```

the tokenId could be a ERC721 NFT or a ERC1155 NFT, but there is no input validation about the markerAsk.amount

In we look into the OrderTypes supported by LooksRare:

```solidity
@looksrare/contracts-exchange-v1/contracts/libraries/OrderTypes.sol
```

```solidity
struct MakerOrder {
	bool isOrderAsk; // true --> ask / false --> bid
	address signer; // signer of the maker order
	address collection; // collection address
	uint256 price; // price (used as )
	uint256 tokenId; // id of the token
	uint256 amount; // amount of tokens to sell/purchase (must be 1 for ERC721, 1+ for ERC1155)
	address strategy; // strategy for trade execution (e.g., DutchAuction, StandardSaleForFixedPrice)
	address currency; // currency (e.g., WETH)
	uint256 nonce; // order nonce (must be unique unless new maker order is meant to override existing one e.g., lower ask price)
	uint256 startTime; // startTime in timestamp
	uint256 endTime; // endTime in timestamp
	uint256 minPercentageToAsk; // slippage protection (9000 --> 90% of the final price must return to ask)
	bytes params; // additional parameters
	uint8 v; // v: parameter (27 or 28)
	bytes32 r; // r: parameter
	bytes32 s; // s: parameter
}
```

We can focus on this line:

```solidity
uint256 amount; // amount of tokens to sell/purchase (must be 1 for ERC721, 1+ for ERC1155)
```

We are missing this validation here to make sure it if the NFT is ERC721, check the amount is 1, if the ERC1155, check the amount is larger than 1.

# [L-04] Floating pragma solidity version

### Line Of Code

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IReentrancyGuard.sol#L2

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IOwnableTwoSteps.sol#L2  

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/ISignatureChecker.sol#L2

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L2   

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L3

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L2

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L2

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelETH.sol#L2

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC20.sol#L2

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC721.sol#L2

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC1155.sol#L2

### Vulnerability detail and recommended fix

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

# [L-05] Update a more updated solidity version.

### Line Of Code

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IReentrancyGuard.sol#L2

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IOwnableTwoSteps.sol#L2  

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/ISignatureChecker.sol#L2

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L2   

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L3

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L2

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L2

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelETH.sol#L2

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC20.sol#L2

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC721.sol#L2

https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC1155.sol#L2

### Vulnerability detail and recommended fix

When some contract use 0.8.17 the most updated solidity version, other use ^0.8.0 or ^0.8.14.

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(,) Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(,) Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions







