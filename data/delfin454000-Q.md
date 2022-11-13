## QA Report: low-risk
___

### Unused/empty `receive()/fallback()` function
Leaving the `receive()` below without a `revert` could result in a loss of funds for a user who sends Ether to the contract (having no way to get anything back)

[SeaportProxy.sol: L86](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L86)
```solidity
    receive() external payable {}
```
Similarly for the following: 

[LooksRareAggregator.sol: L220](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L220)
___
___


### Missing check for address(0x0) when assigning value to address state variable

Check for address(0x0) is missing for `_aggregator`:

[LooksRareProxy.sol: L39](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L39)
```solidity
        aggregator = _aggregator;
```
Similarly for [SeaportProxy.sol: L47](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L47)
___
___


### All contracts should use the same `solidity pragma` version
At the moment, most contracts use 0.8.17 (up-to-date version) but some are fixed to ^0.8.14 or ^0.8.0, as shown below

___
[ReentrancyGuard.sol: L2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/ReentrancyGuard.sol#L2)
```solidity
pragma solidity ^0.8.14;
```
The following also use `pragma solidity ^0.8.14`:

[OwnableTwoSteps.sol: L2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L2)

[SignatureChecker.sol: L2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/SignatureChecker.sol#L2)

[LowLevelERC20Approve.sol: L2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L2)

[LowLevelERC20Transfer.sol: L3](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L3)

[LowLevelERC721Transfer.sol: L4](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L4)

[LowLevelERC1155Transfer.sol: L2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L2)

[LowLevelETH.sol: L2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol#L2)

___
[IERC20.sol: L2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC20.sol#L2)
```solidity
pragma solidity ^0.8.0;
```
The following also use `pragma solidity ^0.8.0`:

[IERC721.sol: L2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC721.sol#L2)

[IERC1155.sol: L2](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC1155.sol#L2)
___
___


## QA Report: non-critical

### Named return variables not used

___
Below, the named return variable (`newCounter`) is never used. In fact, `function incrementCounter()` never returns. 

[SeaportInterface.sol: L343-349](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/SeaportInterface.sol#L343-L349)
```solidity
     * @notice Cancel all orders from a given offerer with a given zone in bulk
     *         by incrementing a counter. Note that only the offerer may
     *         increment the counter.
     *
     * @return newCounter The new counter.
     */
    function incrementCounter() external returns (uint256 newCounter);
```
___
Below, the named return variables (`version`, `domainSeparator` and `conduitController`) are never used. `function information()` never returns. 

[SeaportInterface.sol: L397-410](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/SeaportInterface.sol#L397-L410)
```solidity
     * @notice Retrieve configuration information for this contract.
     *
     * @return version           The contract version.
     * @return domainSeparator   The domain separator for this contract.
     * @return conduitController The conduit Controller set for this contract.
     */
    function information()
        external
        view
        returns (
            string memory version,
            bytes32 domainSeparator,
            address conduitController
        );
```
___
Below, the named return variable (`contractName`) is never used. `function name()` never returns. 

[SeaportInterface.sol: L413-417](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/SeaportInterface.sol#L413-L417)
```solidity
     * @notice Retrieve the name of this contract.
     *
     * @return contractName The name of this contract.
     */
    function name() external view returns (string memory contractName);
```
___
___

### Extra-long single-line comments 
In theory, comments over 80 characters should wrap using multi-line comment syntax. Even if longer comments (up to 120 characters) are acceptable and a scroll bar is provided, very long comments can interfere with readability. 

Some of the long comments in `LooksRare Aggregator` do wrap but the treatment of long comments is inconsistent. Also, long spaces within some comments elongate them unnecessarily. Below, I divide the long comments in the contest into two types: NatSpec statements and lines that include both code and comments

___
__Type 1: Long NatSpec statements__

___
[OwnableTwoSteps.sol: L8-9](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L8-L9)
```solidity
 * @notice This contract offers transfer of ownership in two steps with potential owner having to confirm the transaction.
 *         Renouncement of the ownership is also a two-step process with a timelock since the next potential owner is address(0).
```
Suggestion:
```solidity
 * @notice This contract offers transfer of ownership in two steps with potential owner 
 *     having to confirm the transaction. Renouncement of the ownership is also a
 *     two-step process with a timelock since the next potential owner is address(0).
```
___
[SignatureChecker.sol: L9](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/SignatureChecker.sol#L9)
```solidity
 * @notice This contract is used to verify signatures for EOAs (with length of both 65 and 64 bytes) and contracts (ERC-1271).
```
Suggestion:
```solidity
 * @notice This contract is used to verify signatures for EOAs (with length of  
 *     both 65 and 64 bytes) and contracts (ERC-1271).
```
___
[LowLevelETH.sol: L52](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelETH.sol#L52)
```solidity
     * @notice Return ETH to the original sender if any is left in the payable call but leave 1 wei of ETH in the contract.
```
Suggestion:
```solidity
     * @notice Return ETH to the original sender if any is left in the  
     *     payable call but leave 1 wei of ETH in the contract.
```
___

__Type 2: Extra-long lines that include both code and comments__

If a line containing a comment is longer than 120 characters, it makes sense to put the comment in the line above, as shown

___
[SeaportProxy.sol: L27](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L27)
```solidity
        FulfillmentComponent[][] considerationFulfillments; // Contains the order and item index of each consideration item
```
Suggestion:
```solidity
        // Contains the order and item index of each consideration item
        FulfillmentComponent[][] considerationFulfillments; 
```
___
[SeaportProxy.sol: L35](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L35)
```solidity
        bytes32 zoneHash; // An arbitrary 32-byte value that will be supplied to the zone when fulfilling restricted orders that the zone can utilize when making a determination on whether to authorize the order
```
Suggestion:
```solidity
        // id -> Reference ID for a credit line provided by a single Lender for a given token on a Line of Credit
        bytes32 zoneHash;
```
___
[SeaportProxy.sol: L37](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L37)
```solidity
        bytes32 conduitKey; // A bytes32 value that indicates what conduit, if any, should be utilized as a source for token approvals when performing transfers
```
Suggestion:
```solidity
        // A bytes32 value that indicates what conduit, if any, should be utilized as
        //     a source for token approvals when performing transfers.
        bytes32 conduitKey; 
```
___
___


### Duplicate import
`import {LooksRareProxy}` occurs twice in `LooksRareAggregator.sol`, thereby reducing the readability of the code

[LooksRareAggregator.sol: L4-17](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L4-L17)
```solidity
import {ReentrancyGuard} from "./ReentrancyGuard.sol";
import {LowLevelERC20Approve} from "./lowLevelCallers/LowLevelERC20Approve.sol";
import {LowLevelERC721Transfer} from "./lowLevelCallers/LowLevelERC721Transfer.sol";
import {LowLevelERC1155Transfer} from "./lowLevelCallers/LowLevelERC1155Transfer.sol";
import {IERC20} from "./interfaces/IERC20.sol";
import {LooksRareProxy} from "./proxies/LooksRareProxy.sol";
import {BasicOrder, TokenTransfer} from "./libraries/OrderStructs.sol";
import {TokenRescuer} from "./TokenRescuer.sol";
import {TokenReceiver} from "./TokenReceiver.sol";
import {ILooksRareAggregator} from "./interfaces/ILooksRareAggregator.sol";
import {BasicOrder, FeeData, TokenTransfer} from "./libraries/OrderStructs.sol";
import {LooksRareProxy} from "./proxies/LooksRareProxy.sol";
import {TokenReceiver} from "./TokenReceiver.sol";
import {TokenRescuer} from "./TokenRescuer.sol";
```
___
___


### Event is missing `indexed` fields
Each `event` should use three `indexed` fields if there are three or more fields

___
[ILooksRareAggregator.sol: L44](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/ILooksRareAggregator.sol#L44)
```solidity
    event FeeUpdated(address proxy, uint256 bp, address recipient);
```
___
[IERC20.sol: L5](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC20.sol#L5)
```solidity
    event Transfer(address indexed from, address indexed to, uint256 value);
```
___
[IERC20.sol: L7](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC20.sol#L7)
```solidity
    event Approval(address indexed owner, address indexed spender, uint256 value);
```
___
[IERC721.sol: L9](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC721.sol#L9)
```solidity
    event ApprovalForAll(address indexed owner, address indexed operator, bool approved);
```
___
[IERC1155.sol: L15](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC1155.sol#L15)
```solidity
    event ApprovalForAll(address indexed account, address indexed operator, bool approved);
```
___
___


### NatSpec statement missing for `function` 
Individual NatSpec statements are missing for the following `external` functions (NatSpec is not required for `internal` functions)
___
[OrderStructs.sol: L6-17](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/libraries/OrderStructs.sol#L6-L17)
```solidity
    /**
     * @notice Execute LooksRare NFT sweeps in a single transaction
     * @dev extraData, feeBp and feeRecipient are not used
     * @param orders Orders to be executed by LooksRare
     * @param ordersExtraData Extra data for each order
     * @param recipient The address to receive the purchased NFTs
     * @param isAtomic Flag to enable atomic trades (all or nothing) or partial trades
     */
    function execute(
        BasicOrder[] calldata orders,
        bytes[] calldata ordersExtraData,
        bytes memory,
        address recipient,
        bool isAtomic,
        uint256,
        address
    ) external payable override {
```
Missing: `@param memory`
___
[LooksRareAggregator.sol: L148-157](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L148-L157)
```solidity
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
```
Missing: `@notice`
___
[LooksRareAggregator.sol: L179-184](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L179-L184)
```solidity
    /**
     * @param proxy The marketplace proxy's address
     * @param selector The marketplace proxy's function selector
     * @return Whether the marketplace proxy's function can be called from the aggregator
     */
    function supportsProxyFunction(address proxy, bytes4 selector) external view returns (bool) {
```
Missing: `@notice`
___
___

### NatSpec statement missing for `constructor` 

The `constructor` below is missing an `@notice` statement (a statement which would explain its purpose)

[LooksRareProxy.sol: L33-37](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L33-L37)
```solidity
    /**
     * @param _marketplace LooksRareExchange's address
     * @param _aggregator LooksRareAggregator's address
     */
    constructor(address _marketplace, address _aggregator) {
```
Similarly for the following `constructors`:

[SeaportProxy.sol: L41-45](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L41-L45)

[ERC20EnabledLooksRareAggregator.sol: L18-21](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/ERC20EnabledLooksRareAggregator.sol#L18-L21)
___
___

### NatSpec statement missing for `struct` 

The `struct` below is missing an `@dev` statement and associated explanations such as are given for other `structs`in the contest (see, for example, [ConsiderationStructs.sol: L13-34](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/libraries/seaport/ConsiderationStructs.sol#L13-L34))

[OrderStructs.sol: L6-17](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/libraries/OrderStructs.sol#L6-L17)
```solidity
struct BasicOrder {
  address signer; // The order's maker
  address collection; // The address of the ERC721/ERC1155 token to be purchased
  CollectionType collectionType; // 0 for ERC721, 1 for ERC1155
  uint256[] tokenIds; // The IDs of the tokens to be purchased
  uint256[] amounts; // Always 1 when ERC721, can be > 1 if ERC1155
  uint256 price; // The *taker bid* price to pay for the order
  address currency; // The order's currency, address(0) for ETH
  uint256 startTime; // The timestamp when the order starts becoming valid
  uint256 endTime; // The timestamp when the order stops becoming valid
  bytes signature; // split to v,r,s for LooksRare
}
```
Similarly for the following `structs`:

[OrderStructs.sol: L19-22](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/libraries/OrderStructs.sol#L19-L22)

[OrderStructs.sol: L24-27](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/libraries/OrderStructs.sol#L19-L22)

[SeaportProxy.sol: L25-28](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L25-L28)

[SeaportProxy.sol: L30-39](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L30-L39)
___
___
