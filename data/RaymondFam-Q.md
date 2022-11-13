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
All other instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L45-L48
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L21-L23

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
Each of the following try/catch instances has an empty catch block when making an external function call. Consider refactoring them to correspondingly emit their anticipated logged error.

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
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L198-L217

```
            try
                marketplace.fulfillAdvancedOrder{value: currency == address(0) ? price : 0}(
                    advancedOrder,
                    new CriteriaResolver[](0),
                    bytes32(0),
                    recipient
                )
            {
                if (feeRecipient != address(0)) {
                    uint256 orderFee = (price * feeBp) / 10000;
                    if (currency == lastOrderCurrency) {
                        fee += orderFee;
                    } else {
                        if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);

                        lastOrderCurrency = currency;
                        fee = orderFee;
                    }
                }
            } catch {}
```

## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L107-L134
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L88-L269
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L51-L112
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L220-L251
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenReceiver.sol

## Use of `ecrecover` is Susceptible to Signature Malleability
The built-in EVM pre-compiled `ecrecover` featured in `SignatureChecker.sol`(https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol) is susceptible to signature malleability due to non-unique v and s (which may not always be in the lower half of the modulo set) values, possibly leading to replay attacks. Elsewhere if devoid of the adoption of nonces, this could prove a vulnerability when not carefully used.

Consider using OpenZeppelin’s ECDSA library which has been time tested in preventing this malleability where possible.

## Lines Too Long
Lines in source code are typically limited to 80 characters, but it’s reasonable to stretch beyond this limit when need be as monitor screens theses days are comparatively larger. Considering the files will most likely reside in GitHub that will have a scroll bar automatically kick in when the length is over 164 characters, all code lines and comments should be split when/before hitting this length. Keep line width to max 120 characters for better readability where possible. Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L35
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L37

## Function Calls in Loop Could Lead to Denial of Service
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to the gas limitations or failed transactions. Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L102
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L126
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L145
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L187
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L255
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L41

Consider bounding the loop where possible to avoid unnecessary gas wastage and denial of service.

## Failed Function Call Could Occur Without Contract Existence Check
Performing a low-level calls without confirming contract’s existence (not yet deployed or have been destructed) could return success even though no function call was executed. Here are the four contract instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol

## Not Completely Using OpenZeppelin Contracts
OpenZeppelin maintains a library of standard, audited, community-reviewed, and battle-tested smart contracts. Instead of always importing these contracts, the protocol project re-implements them in some cases. This increases the amount of code that the protocol team will have to maintain and miss all the improvements and bug fixes that the OpenZeppelin team is constantly implementing with the help of the community.

Consider importing the OpenZeppelin contracts instead of re-implementing or copying them. These contracts can be extended to add the extra functionalities required if need be.

Here is one of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ReentrancyGuard.sol#L6-L10

```
/**
 * @title ReentrancyGuard
 * @notice This contract protects against reentrancy attacks.
 *         It is adjusted from OpenZeppelin.
 */
```

## Empty Event
The following event has no parameter in it to emit anything. Consider refactoring or removing this unusable line of code.

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol#L36

```
    event ERC20EnabledLooksRareAggregatorSet();
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L123

```
        emit ERC20EnabledLooksRareAggregatorSet();
```
## Events Associated With Setter Functions
Consider having events associated with setter functions emit both the new and old values instead of just the new value. Here is one of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L162

```
        emit FeeUpdated(proxy, bp, recipient);
```
## `block.timestamp` Unreliable
The use of `block.timestamp` as part of the time checks can be slightly altered by miners/validators to favor them in contracts that have logic strongly dependent on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L70

```
        if (block.timestamp < earliestOwnershipRenouncementTime) revert RenouncementTooEarly();
```

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L114

```
        earliestOwnershipRenouncementTime = block.timestamp + delay;
```
## Unspecific Compiler Version Pragma
For some source-units, the compiler version pragma is very unspecific (^0.8.0 or ^0.8.14). While this often makes sense for libraries to allow them to be included with multiple different versions of an application, it may be a security risk for the actual application implementation itself. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up actually checking a different EVM compilation that is ultimately deployed on the blockchain.

Avoid floating pragmas where possible. It is highly recommend pinning a concrete compiler version (latest without security issues) in at least the top-level “deployed” contracts to make it unambiguous which compiler version is being used. Rule of thumb: a flattened source-unit should have at least one non-floating concrete solidity compiler version pragma.

Here are the contract instances entailed:

`^0.8.0`
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC721.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC1155.sol

`^0.8.14`
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ReentrancyGuard.sol

## Add a Timelock to Critical Parameter Change
It is a good practice to give time for users to react and adjust to critical changes with a mandatory time window between them. The first step merely broadcasts to users that a particular change is coming, and the second step commits that change after a suitable waiting period. This allows users that do not accept the change to withdraw within the grace period. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious Owner making any malicious or ulterior intention). Specifically, privileged roles could use front running to make malicious changes just ahead of incoming transactions, or purely accidental negative effects could occur due to the unfortunate timing of changes. 

Consider extending the timelock feature beyond contract ownership management to business critical functions. Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L132

```
    function addFunction(address proxy, bytes4 selector) external onlyOwner {
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L143

```
    function removeFunction(address proxy, bytes4 selector) external onlyOwner {
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L153-L157

```
    function setFee(
        address proxy,
        uint256 bp,
        address recipient
    ) external onlyOwner {
```
## Contract Owner Has Too Many Privileges
The owner of the contracts has too many privileges relative to standard users. The consequence is disastrous if the contract owner's private key has been compromised. And, in the event the key was lost or unrecoverable, no implementation upgrades and system parameter updates will ever be possible.

For a project this grand, it increases the likelihood that the owner will be targeted by an attacker, especially given the insufficient protection on sensitive owner private keys. The concentration of privileges creates a single point of failure; and, here are some of the incidents that could possibly transpire:

Transfer ownership and mess up with all the setter functions, hijacking the entire protocol.

Consider:
1) splitting privileges (e.g. via the multisig option) to ensure that no one address has excessive ownership of the system,
2) clearly documenting the functions and implementations the owner can change,
3) documenting the risks associated with privileged users and single points of failure, and
4) ensuring that users are aware of all the risks associated with the system.

## Added Measures When Calling Seaport Functions
A high risk edge case bug associated with the Seaport `_validateOrderAndUpdateStatus()` was found in the [May code4rena audit contest.](https://code4rena.com/reports/2022-05-opensea-seaport#h-01-truncation-in-ordervalidator-can-lead-to-resetting-the-fill-and-selling-more-tokens) It concerns truncation to zero on both the numerator and the denominator particularly when involving a restricted token sale. The mitigation steps recommended was not deemed ideal then, and although the Seaport protocol team has since fixed this bug with added GCD measure and removing `unchecked {...}`, it is recommended adding the complementary check to circumvent any other hidden issues associated with it whilst having the error detected at its earliest possibility.

For instance, instead of allowing user to input `2**118` and  `2**119` as numerator and denominator for an intended fraction of `1/2`, stem it by making sure that those two inputs could not be more than the total ERC721/1155 collection availability and multiply them with factor just enough to remove the decimals. Here are the two for loop instances entailed prior to calling `marketplace.fulfillAvailableAdvancedOrders()` and `marketplace.fulfillAdvancedOrder()`:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L114
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L187-L193



