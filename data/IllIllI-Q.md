## Summary


### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Unused/empty `receive()`/`fallback()` function | 2 |
| [L&#x2011;02] | Missing checks for `address(0x0)` when assigning values to `address` state variables | 4 |
| [L&#x2011;03] | Library function is vulnerable to signature malleability attacks | 1 |

Total: 7 instances over 3 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Duplicate import statements | 4 |
| [N&#x2011;02] | Contract implements interface without extending the interface | 1 |
| [N&#x2011;03] | `constant`s should be defined rather than using magic numbers | 15 |
| [N&#x2011;04] | Constant redefined elsewhere | 3 |
| [N&#x2011;05] | Lines are too long | 2 |
| [N&#x2011;06] | Non-library/interface files should use fixed compiler versions, not floating ones | 5 |
| [N&#x2011;07] | File is missing NatSpec | 7 |
| [N&#x2011;08] | NatSpec is incomplete | 4 |
| [N&#x2011;09] | Event is missing `indexed` fields | 8 |

Total: 49 instances over 9 issues

## Low Risk Issues

### [L&#x2011;01]  Unused/empty `receive()`/`fallback()` function
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. `require(msg.sender == address(weth))`). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds

*There are 2 instances of this issue:*
```solidity
File: contracts/LooksRareAggregator.sol

222:      receive() external payable {}

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L222

```solidity
File: contracts/proxies/SeaportProxy.sol

86:       receive() external payable {}

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L86

### [L&#x2011;02]  Missing checks for `address(0x0)` when assigning values to `address` state variables

*There are 4 instances of this issue:*
```solidity
File: contracts/LooksRareAggregator.sol

122:          erc20EnabledLooksRareAggregator = _erc20EnabledLooksRareAggregator;

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L122

```solidity
File: contracts/OwnableTwoSteps.sol

102:          potentialOwner = newPotentialOwner;

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L102

```solidity
File: contracts/proxies/LooksRareProxy.sol

39:           aggregator = _aggregator;

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L39

```solidity
File: contracts/proxies/SeaportProxy.sol

47:           aggregator = _aggregator;

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L47

### [L&#x2011;03]  Library function is vulnerable to signature malleability attacks
Use OpenZeppelin's `ECDSA` contract rather than calling `ecrecover()` directly

*There is 1 instance of this issue:*
```solidity
File: /contracts/SignatureChecker.sol

56       function _recoverEOASigner(bytes32 hash, bytes memory signature) internal pure returns (address signer) {
57           (bytes32 r, bytes32 s, uint8 v) = _splitSignature(signature);
58   
59           // If the signature is valid (and not malleable), return the signer address
60           signer = ecrecover(hash, v, r, s);
61   
62           if (signer == address(0)) revert NullSignerAddress();
63:      }

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/SignatureChecker.sol#L56-L63


## Non-critical Issues

### [N&#x2011;01]  Duplicate import statements

*There are 4 instances of this issue:*
```solidity
File: contracts/LooksRareAggregator.sol

14:   import {BasicOrder, FeeData, TokenTransfer} from "./libraries/OrderStructs.sol";

15:   import {LooksRareProxy} from "./proxies/LooksRareProxy.sol";

16:   import {TokenReceiver} from "./TokenReceiver.sol";

17:   import {TokenRescuer} from "./TokenRescuer.sol";

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L14

### [N&#x2011;02]  Contract implements interface without extending the interface
Not extending the interface may lead to the wrong function signature being used, leading to unexpected behavior. If the interface is in fact being implemented, use the `override` keyword to indicate that fact

*There is 1 instance of this issue:*
```solidity
File: contracts/TokenReceiver.sol

/// @audit IERC1155Receiver.onERC1155Received(), IERC1155Receiver.onERC1155BatchReceived()
4:    abstract contract TokenReceiver {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenReceiver.sol#L4

### [N&#x2011;03]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 15 instances of this issue:*
```solidity
File: contracts/LooksRareAggregator.sol

/// @audit 10000
158:          if (bp > 10000) revert FeeTooHigh();

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L158

```solidity
File: contracts/proxies/SeaportProxy.sol

/// @audit 10000
147:              uint256 orderFee = (orders[i].price * feeBp) / 10000;

/// @audit 10000
207:                      uint256 orderFee = (price * feeBp) / 10000;

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L147

```solidity
File: contracts/SignatureChecker.sol

/// @audit 64
28:           if (signature.length == 64) {

/// @audit 0x20
31:                   r := mload(add(signature, 0x20))

/// @audit 0x40
32:                   vs := mload(add(signature, 0x40))

/// @audit 0x7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
33:                   s := and(vs, 0x7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff)

/// @audit 65
36:           } else if (signature.length == 65) {

/// @audit 0x20
38:                   r := mload(add(signature, 0x20))

/// @audit 0x40
39:                   s := mload(add(signature, 0x40))

/// @audit 0x60
40:                   v := byte(0, mload(add(signature, 0x60)))

/// @audit 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0
46:           if (uint256(s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0) revert BadSignatureS();

/// @audit 27
/// @audit 28
48:           if (v != 27 && v != 28) revert BadSignatureV(v);

/// @audit 0x1626ba7e
80:               if (IERC1271(signer).isValidSignature(hash, signature) != 0x1626ba7e) revert InvalidSignatureERC1271();

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/SignatureChecker.sol#L28

### [N&#x2011;04]  Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

*There are 3 instances of this issue:*
```solidity
File: contracts/proxies/LooksRareProxy.sol

/// @audit seen in contracts/ERC20EnabledLooksRareAggregator.sol 
31:       address public immutable aggregator;

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L31

```solidity
File: contracts/proxies/SeaportProxy.sol

/// @audit seen in contracts/proxies/LooksRareProxy.sol 
20:       SeaportInterface public immutable marketplace;

/// @audit seen in contracts/proxies/LooksRareProxy.sol 
21:       address public immutable aggregator;

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L20

### [N&#x2011;05]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There are 2 instances of this issue:*
```solidity
File: contracts/proxies/SeaportProxy.sol

8:    import {AdvancedOrder, CriteriaResolver, OrderParameters, OfferItem, ConsiderationItem, FulfillmentComponent, AdditionalRecipient} from "../libraries/seaport/ConsiderationStructs.sol";

35:           bytes32 zoneHash; // An arbitrary 32-byte value that will be supplied to the zone when fulfilling restricted orders that the zone can utilize when making a determination on whether to authorize the order

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L8

### [N&#x2011;06]  Non-library/interface files should use fixed compiler versions, not floating ones

*There are 5 instances of this issue:*
```solidity
File: contracts/lowLevelCallers/LowLevelERC1155Transfer.sol

2:    pragma solidity ^0.8.14;

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L2

```solidity
File: contracts/lowLevelCallers/LowLevelERC20Approve.sol

2:    pragma solidity ^0.8.14;

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L2

```solidity
File: contracts/lowLevelCallers/LowLevelERC20Transfer.sol

4:    pragma solidity ^0.8.14;

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L4

```solidity
File: contracts/lowLevelCallers/LowLevelERC721Transfer.sol

2:    pragma solidity ^0.8.14;

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L2

```solidity
File: contracts/lowLevelCallers/LowLevelETH.sol

2:    pragma solidity ^0.8.14;

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelETH.sol#L2

### [N&#x2011;07]  File is missing NatSpec

*There are 7 instances of this issue:*
```solidity
File: contracts/interfaces/IERC1155.sol


```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC1155.sol

```solidity
File: contracts/interfaces/IERC20.sol


```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC20.sol

```solidity
File: contracts/interfaces/IERC721.sol


```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC721.sol

```solidity
File: contracts/libraries/OrderEnums.sol


```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/libraries/OrderEnums.sol

```solidity
File: contracts/libraries/OrderStructs.sol


```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/libraries/OrderStructs.sol

```solidity
File: contracts/libraries/seaport/ConsiderationEnums.sol


```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/libraries/seaport/ConsiderationEnums.sol

```solidity
File: contracts/TokenReceiver.sol


```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenReceiver.sol

### [N&#x2011;08]  NatSpec is incomplete

*There are 4 instances of this issue:*
```solidity
File: contracts/proxies/LooksRareProxy.sol

/// @audit Missing: '@param bytes'
/// @audit Missing: '@param uint256'
/// @audit Missing: '@param address'
42        /**
43         * @notice Execute LooksRare NFT sweeps in a single transaction
44         * @dev extraData, feeBp and feeRecipient are not used
45         * @param orders Orders to be executed by LooksRare
46         * @param ordersExtraData Extra data for each order
47         * @param recipient The address to receive the purchased NFTs
48         * @param isAtomic Flag to enable atomic trades (all or nothing) or partial trades
49         */
50        function execute(
51            BasicOrder[] calldata orders,
52            bytes[] calldata ordersExtraData,
53            bytes memory,
54            address recipient,
55            bool isAtomic,
56            uint256,
57:           address

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L42-L57

```solidity
File: contracts/SignatureChecker.sol

/// @audit Missing: '@return'
54         * @param signature Bytes containing the signature (64 or 65 bytes)
55         */
56:       function _recoverEOASigner(bytes32 hash, bytes memory signature) internal pure returns (address signer) {

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/SignatureChecker.sol#L54-L56

### [N&#x2011;09]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 8 instances of this issue:*
```solidity
File: contracts/interfaces/IERC1155.sol

15:       event ApprovalForAll(address indexed account, address indexed operator, bool approved);

17:       event URI(string value, uint256 indexed id);

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC1155.sol#L15

```solidity
File: contracts/interfaces/IERC20.sol

5:        event Transfer(address indexed from, address indexed to, uint256 value);

7:        event Approval(address indexed owner, address indexed spender, uint256 value);

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC20.sol#L5

```solidity
File: contracts/interfaces/IERC721.sol

9:        event ApprovalForAll(address indexed owner, address indexed operator, bool approved);

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC721.sol#L9

```solidity
File: contracts/interfaces/ILooksRareAggregator.sol

44:       event FeeUpdated(address proxy, uint256 bp, address recipient);

51:       event FunctionAdded(address indexed proxy, bytes4 selector);

58:       event FunctionRemoved(address indexed proxy, bytes4 selector);

```
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/ILooksRareAggregator.sol#L44

