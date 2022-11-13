## USE OF BLOCK.TIMESTAMP
Some contract code uses the block.timestamp as part of the calculations and time checks. Nevertheless, timestamps can be slightly altered by miners/validators to favor them in contracts that have logic that depends strongly on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future. Here are the two instances found.

[OwnableTwoSteps.sol: Line 70](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L70)

```
        if (block.timestamp < earliestOwnershipRenouncementTime) revert RenouncementTooEarly();
```
[OwnableTwoSteps.sol: Line 114](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L114)

```
        earliestOwnershipRenouncementTime = block.timestamp + delay;
```
## COMMENT AND CODE LINE LENGTH
Try limit the length of comments and/or code lines to 80 - 100 character long for readability sake. There are two instances found.

[SeaportProxy.sol: Line 35 - 37](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L35-L37)

## TIMELOCK FOR CRITICAL PARAMETER CHANGE
It is a good practice giving time to users to react and adjust to critical changes with a mandatory time window between the changes. The first step is simply broadcasting to users with a specific change that is coming whilst the second step commits that change after an appropriate period of waiting. This would allow time for users opposing to the change to withdraw within the set time frame. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of the owner making a malicious act). Specifically, privileged roles could use front running to make malicious changes just ahead of incoming transactions, or purely accidental negative effects could occur due to the unfortunate timing of changes.

It is recommended extending the timelock feature to all business critical functions instead of just the contract ownership management. Here are the three instances found.

[LooksRareAggregator: Line 132](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L132)

```
    function addFunction(address proxy, bytes4 selector) external onlyOwner {
```
[LooksRareAggregator: Line 143](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L143)

```
    function removeFunction(address proxy, bytes4 selector) external onlyOwner {
```
[LooksRareAggregator: Lines 153 - 157](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L153-L157)

```
    function setFee(
        address proxy,
        uint256 bp,
        address recipient
    ) external onlyOwner {
```
## MISSING NATSPEC
As documented in the link below:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

It is recommended using a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more.

Here are some of the instances found.

[TokenReceiver.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenReceiver.sol)

[LooksRareAggregator.sol: Lines 220 - 251](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L220-L251)

```
    receive() external payable {}

    function _encodeCalldataAndValidateFeeBp(
        TradeData calldata singleTradeData,
        address recipient,
        bool isAtomic
    ) private view returns (bytes memory proxyCalldata, bool maxFeeBpViolated) {
        FeeData memory feeData = _proxyFeeData[singleTradeData.proxy];
        maxFeeBpViolated = singleTradeData.maxFeeBp < feeData.bp;
        proxyCalldata = abi.encodeWithSelector(
            singleTradeData.selector,
            singleTradeData.orders,
            singleTradeData.ordersExtraData,
            singleTradeData.extraData,
            recipient,
            isAtomic,
            feeData.bp,
            feeData.recipient
        );
    }

    function _returnERC20TokensIfAny(TokenTransfer[] calldata tokenTransfers, address recipient) private {
        uint256 tokenTransfersLength = tokenTransfers.length;
        for (uint256 i; i < tokenTransfersLength; ) {
            uint256 balance = IERC20(tokenTransfers[i].currency).balanceOf(address(this));
            if (balance > 0) _executeERC20DirectTransfer(tokenTransfers[i].currency, recipient, balance);

            unchecked {
                ++i;
            }
        }
    }
```
[LooksRareProxy.sol: Lines 107 - 134](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L107-L134)

```
    function _executeSingleOrder(
        OrderTypes.TakerOrder memory takerBid,
        OrderTypes.MakerOrder memory makerAsk,
        address recipient,
        CollectionType collectionType,
        bool isAtomic
    ) private {
        if (isAtomic) {
            marketplace.matchAskWithTakerBidUsingETHAndWETH{value: takerBid.price}(takerBid, makerAsk);
            _transferTokenToRecipient(
                collectionType,
                recipient,
                makerAsk.collection,
                makerAsk.tokenId,
                makerAsk.amount
            );
        } else {
            try marketplace.matchAskWithTakerBidUsingETHAndWETH{value: takerBid.price}(takerBid, makerAsk) {
                _transferTokenToRecipient(
                    collectionType,
                    recipient,
                    makerAsk.collection,
                    makerAsk.tokenId,
                    makerAsk.amount
                );
            } catch {}
        }
    }
```
## SANITY CHECKS
Zero address checks implemented at the constructor could avoid human errors leading to non-functional calls associated with the mistakes. This is especially so when the incidents entail immutable variables preventing them from getting reassigned that could end up having the protocol redeploy the contract.

Here are the three instances found.

[LooksRareProxy.sol: Lines 37 - 40](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L37-L40)

```
    constructor(address _marketplace, address _aggregator) {
        marketplace = ILooksRareExchange(_marketplace);
        aggregator = _aggregator;
    }
```
[SeaportProxy.sol: Lines 45 - 48](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L45-L48)

```
    constructor(address _marketplace, address _aggregator) {
        marketplace = SeaportInterface(_marketplace);
        aggregator = _aggregator;
    }
```
[ERC20EnabledLooksRareAggregator.sol: Lines 21 - 23](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L21-L23)

```
    constructor(address _aggregator) {
        aggregator = ILooksRareAggregator(_aggregator);
    }
```
## USE OF DESCRIPTIVE VARIABLE NAMES
Non-descriptive local variables could make code base difficult to read and navigate. Here are some of the instance found.

[ILooksRareAggregator.sol: Line 44](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol#L44)

```
    @ `bp` should be changed to something like `feeBasisPoint`
    event FeeUpdated(address proxy, uint256 bp, address recipient);
```