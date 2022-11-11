## Un-indexed Parameters in Events
Consider indexing parameters for events, serving as logs filter when looking for specifically wanted data. Up to three parameters in an event function can receive the attribute `indexed` which will cause the respective arguments to be treated as log topics instead of data. Here is one of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol#L44

```
    event FeeUpdated(address proxy, uint256 bp, address recipient);
```
## Descriptive and Verbose Options
Consider making the naming of local variables more verbose and descriptive so all other peer developers would better be able to comprehend the intended statement logic, significantly enhancing the code readability. Here is one of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol#L44

```
    @ bp
    event FeeUpdated(address proxy, uint256 bp, address recipient);
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/OrderStructs.sol#L25

```
    @ bp
    uint256 bp; // Aggregator fee basis point
```

## Typo Mistakes
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/seaport/ConsiderationStructs.sol#L168

```
    @ are
 *      type is restricted and the offerer or zone are not the caller.
```
## Sanity/Threshold/Limit Checks
Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper `uint256` validation as well as zero address checks in the constructor. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables. If the validation procedure is unclear or too complex to implement on-chain, document the potential issues that could produce invalid values. 

Consider also adding an optional codehash check for immutable address at the constructor since the zero address check cannot guarantee a matching address has been inputted. 

These checks are generally not implemented in the contracts. As an example, the following constructor may be refactored to:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L37-L40

```
    constructor(address _marketplace, address _aggregator, bytes32 _marketplaceCodeHash, bytes32 _aggregatorCodeHash) {
        if (_marketplace == address(0) || _aggregator == address(0) || 
            _marketplace.codehash != _marketplaceCodeHash || _aggregator.codehash != _aggregatorCodeHash) {
            revert InvalidAddress();
        }     
        marketplace = ILooksRareExchange(_marketplace);
        aggregator = _aggregator;
    }
```
## Empty/Unused Function Parameters
Empty or unused function parameters should be commented out as a better and declarative way to silence runtime warning messages. As an example, the following function may have these parameters refactored to:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L50-L58

```
    function execute(
        BasicOrder[] calldata orders,
        bytes[] calldata ordersExtraData,
        bytes memory /* extraData */,
        address recipient,
        bool isAtomic,
        uint256 /* feeBp */,
        address /* feeRecipient */
    ) external payable override {
```
## Empty Catch Block
The following try/catch has an empty catch block when making an external function call. Consider refactoring it to emit its anticipated logged error.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L124-L132

```
            try marketplace.matchAskWithTakerBidUsingETHAndWETH{value: takerBid.price}(takerBid, makerAsk) {
                _transferTokenToRecipient(
                    collectionType,
                    recipient,
                    makerAsk.collection,
                    makerAsk.tokenId,
                    makerAsk.amount
                );
            } catch {}
```
## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L107-L134

## Use of `ecrecover` is Susceptible to Signature Malleability
The built-in EVM pre-compiled `ecrecover` featured in `SignatureChecker.sol`(https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol) is susceptible to signature malleability due to non-unique v and s (which may not always be in the lower half of the modulo set) values, possibly leading to replay attacks. Elsewhere if devoid of the adoption of nonces, this could prove a vulnerability when not carefully used.

Consider using OpenZeppelin’s ECDSA library which has been time tested in preventing this malleability where possible.

