# Report
## Low Risk ##
### [L-1]: Missing checks for address(0x0)
**Context:**

1. ```marketplace = ILooksRareExchange(_marketplace);``` [L38](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L38) 
1. ```aggregator = _aggregator;``` [L39](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L39) 
1. ```marketplace = SeaportInterface(_marketplace);``` [L46](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L46) 
1. ```aggregator = _aggregator;``` [L47](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L47) 
1. ```aggregator = ILooksRareAggregator(_aggregator);``` [L22](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L22) 

**Recommendation:**

Add non-zero address checks when set address state variables.

## Non-Critical Issues ##

### [N-1]: Wrong order of functions
**Context:**

1. [SeaportProxy.receive](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L86) (receive function must be after constructor)
1. [LooksRareAggregator.rescueERC721](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L195) (external function can not go after external view function)
1. [LooksRareAggregator.receive](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L220) (receive function must be after constructor and before all external functions)

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

Within a grouping, place the view and pure functions last.

**Recommendation:**

Put the functions in the correct order according to the documentation.

### [N-2]: NatSpec is missing
**Context:**

1. https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/OrderStructs.sol
1. https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L107
1. https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L88 
1. https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L136 
1. https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L166 
1. https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L178 
1. https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L227 
1. https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L39 
1. https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L222 
1. https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L241 
1. https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenReceiver.sol#L24 
1. https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenTransferrer.sol#L14


### [N-3]: Line is too long
**Context:**

1. ```FulfillmentComponent[][] considerationFulfillments; // Contains the order and item index of each consideration item``` [L27](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L27) 
1. ```bytes32 zoneHash; // An arbitrary 32-byte value that will be supplied to the zone when fulfilling restricted orders that the zone can utilize when making a determination on whether to authorize the order``` [L35](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L35) 
1. ```bytes32 conduitKey; // A bytes32 value that indicates what conduit, if any, should be utilized as a source for token approvals when performing transfers``` [L37](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L37) 
1. ```* @notice This contract offers transfer of ownership in two steps with potential owner having to confirm the transaction.``` [L8](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L8) 
1. ```*         Renouncement of the ownership is also a two-step process with a timelock since the next potential owner is address(0).``` [L9](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L9) 
1. ```* @notice This contract is used to verify signatures for EOAs (with length of both 65 and 64 bytes) and contracts (ERC-1271).``` [L9](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L9) 
1. ```* @notice Return ETH to the original sender if any is left in the payable call but leave 1 wei of ETH in the contract.``` [L52](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelETH.sol#L52) 

**Description:**

Maximum suggested line length is [120 characters](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length).
