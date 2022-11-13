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
[OrderStructs.sol: Line 25](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/OrderStructs.sol#L25)

```
    @ `bp` should be changed to something like `feeBasisPoint`
    uint256 bp; // Aggregator fee basis point
```
## DOS ON UNBOUNDED LOOP
Unbounded loop could lead to OOG (Out of Gas) denying the users' of needed services. Here are some instances found.

[SeaportProxy.sol: Line 102](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L102)

```
        for (uint256 i; i < ordersLength; ) {
```

[SeaportProxy.sol: Line 255](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L255)

```
        for (uint256 j; j < recipientsLength; ) {
```
[ERC20EnabledLooksRareAggregator.sol: Line 41](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L41)

```
        for (uint256 i; i < tokenTransfersLength; ) {
```
## TYPO ERRORS
[ConsiderationStructs.sol: Line 168](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/seaport/ConsiderationStructs.sol#L168)

```
    @ are should be corrected to is
 *      type is restricted and the offerer or zone are not the caller.
```
## COMPILER VERSION PRAGMA SPECIFICITY
Non-library contracts and interfaces should avoid using floating pragmas ^0.8.9. Doing this may be a security risk for the actual application implementation itself. For instance, a known vulnerable compiler version may accidentally be selected or a security tool might fallback to an older compiler version leading to checking a different EVM compilation that is ultimately deployed on the blockchain.

Here are the contract instances found using `^0.8.0`.

[IERC20.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20.sol)
[IERC721.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC721.sol)
[IERC1155.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC1155.sol)

Here are the contract instances found using `^0.8.14`.

[LowLevelETH.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol)
[LowLevelERC1155Transfer.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol)
[LowLevelERC721Transfer.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol)
[LowLevelERC20Transfer.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol)
[LowLevelERC20Approve.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol)
[SignatureChecker.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol)
[OwnableTwoSteps.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol)
[ReentrancyGuard.sol](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ReentrancyGuard.sol)

## EVENT PARAMETERS SHOULD BE INDEXED
Up to three event parameters should be indexed. This will help filter off the logs in listening for specifically wanted data. Using `indexed` has the benefit of making the arguments log topics instead of data.

Here is one of the instances found.

[ILooksRareAggregator.sol: Line 44](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol#L44)

```
    event FeeUpdated(address proxy, uint256 bp, address recipient);
```
## NEW AND OLD VALUES SHOULD BE EMITTED
It is recommended having events associated with setter functions emit both the new and old values instead of just the new value. Here is one of the instances found.

[LooksRareAggregator.sol: Line 162](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L162)

```
        emit FeeUpdated(proxy, bp, recipient);
```
## UNUSABLE EVENT
The following event is empty in the parameter field making it incapable of emitting anything. It is recommended removing these unusable lines of code.

[ILooksRareAggregator.sol: Line 36](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol#L36)

```
    event ERC20EnabledLooksRareAggregatorSet();
```

[LooksRareAggregator.sol: Line 123](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L123)

```
        emit ERC20EnabledLooksRareAggregatorSet();
```
## ECRECOVER SUBJECT TO REPLAY ATTACK
Whenever possible, use OpenZeppelin’s ECDSA library which has been time tested in preventing replay attack of signature malleability associated with `ecrecover'. The issue could arise from the non-unique value of v and an s value that could end up in the upper half of the modulo set. 

Here is the contract instance found.

[SignatureChecker.sol: Line 60](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L60)

```
        signer = ecrecover(hash, v, r, s);
```
## SINGLE POINT OF FAILURE
Contract owner possesses numerous privileges that could prove disastrous if the private key was compromised. If the key was lost/unrecoverable, all needed system parameter updates would be halted.

An established owner is generally prone to target attack, especially given the insufficient protection on sensitive owner private keys. The concentration of privileges creates a single point of failure, leading to transfer of ownership and malicious reset of setter function parameters.

The following measures are recommended.
1) Split privileges using multisig option making sure that no one address has excessive ownership of the system.
2) Clearly document the functions and implementations the owner can change.
3) Document the risks associated with privileged users and single points of failure.
4) Make sure that users are aware of all the risks associated with the system.

## SEAPORT FUNCTION CALLS
A high risk edge case bug associated with the Seaport `_validateOrderAndUpdateStatus()` was found in the [May code4rena audit contest.](https://code4rena.com/reports/2022-05-opensea-seaport#h-01-truncation-in-ordervalidator-can-lead-to-resetting-the-fill-and-selling-more-tokens) It concerns truncation to zero on both the numerator and the denominator particularly when involving a restricted token sale. The mitigation steps recommended was not deemed ideal then, and although the Seaport protocol team has since fixed this bug with added GCD measure and removing `unchecked {...}`, it is recommended adding the complementary check to circumvent any other hidden issues associated with it whilst having the error detected at its earliest possibility.

For instance, instead of allowing user to input `2**118` and  `2**119` as numerator and denominator for an intended fraction of `1/2`, stem it by making sure that those two inputs could not be more than the total ERC721/1155 collection availability and multiply them with factor just enough to remove the decimals. Here are the two for loop instances entailed prior to calling `marketplace.fulfillAvailableAdvancedOrders()` and `marketplace.fulfillAdvancedOrder()`:

[SeaportProxy.sol#L102-L114](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L102-L114)

```
        for (uint256 i; i < ordersLength; ) {
            OrderExtraData memory orderExtraData = abi.decode(ordersExtraData[i], (OrderExtraData));
            advancedOrders[i].parameters = _populateParameters(orders[i], orderExtraData);
            advancedOrders[i].numerator = orderExtraData.numerator;
            advancedOrders[i].denominator = orderExtraData.denominator;
            advancedOrders[i].signature = orders[i].signature;

            if (orders[i].currency == address(0)) ethValue += orders[i].price;

            unchecked {
                ++i;
            }
        }
```

[SeaportProxy.sol#L187-L193](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L187-L193)

```
        for (uint256 i; i < orders.length; ) {
            OrderExtraData memory orderExtraData = abi.decode(ordersExtraData[i], (OrderExtraData));
            AdvancedOrder memory advancedOrder;
            advancedOrder.parameters = _populateParameters(orders[i], orderExtraData);
            advancedOrder.numerator = orderExtraData.numerator;
            advancedOrder.denominator = orderExtraData.denominator;
            advancedOrder.signature = orders[i].signature;
```