### Title
Cache Array Length Outside of Loop

#### Impact
Issue Information: Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Example
🤦 Bad:

for (uint256 i = 0; i < array.length; i++) {
    // invariant: array's length is not changed
}
🚀 Good:

uint256 len = array.length
for (uint256 i = 0; i < len; i++) {
    // invariant: array's length is not changed
}

#### Findings:
```
2022-11-looksrare/contracts/ERC20EnabledLooksRareAggregator.sol::34 => if (tokenTransfers.length == 0) revert UseLooksRareAggregatorDirectly();
2022-11-looksrare/contracts/ERC20EnabledLooksRareAggregator.sol::40 => uint256 tokenTransfersLength = tokenTransfers.length;
2022-11-looksrare/contracts/LooksRareAggregator.sol::59 => uint256 tradeDataLength = tradeData.length;
2022-11-looksrare/contracts/LooksRareAggregator.sol::62 => uint256 tokenTransfersLength = tokenTransfers.length;
2022-11-looksrare/contracts/LooksRareAggregator.sol::92 => if (returnData.length > 0) {
2022-11-looksrare/contracts/LooksRareAggregator.sol::242 => uint256 tokenTransfersLength = tokenTransfers.length;
2022-11-looksrare/contracts/SignatureChecker.sol::9 => * @notice This contract is used to verify signatures for EOAs (with length of both 65 and 64 bytes) and contracts (ERC-1271).
2022-11-looksrare/contracts/SignatureChecker.sol::28 => if (signature.length == 64) {
2022-11-looksrare/contracts/SignatureChecker.sol::36 => } else if (signature.length == 65) {
2022-11-looksrare/contracts/SignatureChecker.sol::43 => revert WrongSignatureLength(signature.length);
2022-11-looksrare/contracts/SignatureChecker.sol::77 => if (signer.code.length == 0) {
2022-11-looksrare/contracts/interfaces/ISignatureChecker.sol::14 => error WrongSignatureLength(uint256 length);
2022-11-looksrare/contracts/libraries/seaport/ConsiderationStructs.sol::120 => // Total length, excluding dynamic array data: 0x264 (580)
2022-11-looksrare/contracts/libraries/seaport/ConsiderationStructs.sol::151 => // offer.length                          // 0x160
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Approve.sol::28 => if (data.length > 0) {
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Transfer.sol::35 => if (data.length > 0) {
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Transfer.sol::54 => if (data.length > 0) {
2022-11-looksrare/contracts/proxies/LooksRareProxy.sol::61 => uint256 ordersLength = orders.length;
2022-11-looksrare/contracts/proxies/LooksRareProxy.sol::62 => if (ordersLength == 0 || ordersLength != ordersExtraData.length) revert InvalidOrderLength();
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::71 => uint256 ordersLength = orders.length;
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::72 => if (ordersLength == 0 || ordersLength != ordersExtraData.length) revert InvalidOrderLength();
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::96 => uint256 ordersLength = orders.length;
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::126 => for (uint256 i; i < availableOrders.length; ) {
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::143 => uint256 ordersLength = orders.length;
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::187 => for (uint256 i; i < orders.length; ) {
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::232 => uint256 recipientsLength = orderExtraData.recipients.length;

```
#### Tools used
Manual





===============================================================================================



### Title
Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

Example
🤦 Bad:

// `a` being of type unsigned integer
require(a > 0, "!a > 0");
🚀 Good:

// `a` being of type unsigned integer
require(a != 0, "!a > 0");
#### Findings:
```
2022-11-looksrare/contracts/LooksRareAggregator.sol::92 => if (returnData.length > 0) {
2022-11-looksrare/contracts/LooksRareAggregator.sol::108 => if (tokenTransfersLength > 0) _returnERC20TokensIfAny(tokenTransfers, originator);
2022-11-looksrare/contracts/LooksRareAggregator.sol::245 => if (balance > 0) _executeERC20DirectTransfer(tokenTransfers[i].currency, recipient, balance);
2022-11-looksrare/contracts/SignatureChecker.sol::46 => if (uint256(s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0) revert BadSignatureS();
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Approve.sol::28 => if (data.length > 0) {
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Transfer.sol::35 => if (data.length > 0) {
2022-11-looksrare/contracts/lowLevelCallers/LowLevelERC20Transfer.sol::54 => if (data.length > 0) {
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::152 => if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::163 => if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::211 => if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::224 => if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
```
#### Tools used
Manual





===============================================================================================


### Title
Long Revert Strings

#### Impact
Issue Information: Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

Example
🤦 Bad:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Good (with shorter string):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Good (with custom errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}

#### Findings:
```
2022-11-looksrare/contracts/ERC20EnabledLooksRareAggregator.sol::4 => import {LowLevelERC20Transfer} from "./lowLevelCallers/LowLevelERC20Transfer.sol";
2022-11-looksrare/contracts/ERC20EnabledLooksRareAggregator.sol::5 => import {IERC20EnabledLooksRareAggregator} from "./interfaces/IERC20EnabledLooksRareAggregator.sol";
2022-11-looksrare/contracts/ERC20EnabledLooksRareAggregator.sol::6 => import {ILooksRareAggregator} from "./interfaces/ILooksRareAggregator.sol";
2022-11-looksrare/contracts/LooksRareAggregator.sol::5 => import {LowLevelERC20Approve} from "./lowLevelCallers/LowLevelERC20Approve.sol";
2022-11-looksrare/contracts/LooksRareAggregator.sol::6 => import {LowLevelERC721Transfer} from "./lowLevelCallers/LowLevelERC721Transfer.sol";
2022-11-looksrare/contracts/LooksRareAggregator.sol::7 => import {LowLevelERC1155Transfer} from "./lowLevelCallers/LowLevelERC1155Transfer.sol";
2022-11-looksrare/contracts/LooksRareAggregator.sol::13 => import {ILooksRareAggregator} from "./interfaces/ILooksRareAggregator.sol";
2022-11-looksrare/contracts/OwnableTwoSteps.sol::4 => import {IOwnableTwoSteps} from "./interfaces/IOwnableTwoSteps.sol";
2022-11-looksrare/contracts/ReentrancyGuard.sol::4 => import {IReentrancyGuard} from "./interfaces/IReentrancyGuard.sol";
2022-11-looksrare/contracts/SignatureChecker.sol::5 => import {ISignatureChecker} from "./interfaces/ISignatureChecker.sol";
2022-11-looksrare/contracts/TokenRescuer.sol::6 => import {LowLevelERC20Transfer} from "./lowLevelCallers/LowLevelERC20Transfer.sol";
2022-11-looksrare/contracts/TokenRescuer.sol::7 => import {LowLevelETH} from "./lowLevelCallers/LowLevelETH.sol";
2022-11-looksrare/contracts/interfaces/SeaportInterface.sol::15 => } from "../libraries/seaport/ConsiderationStructs.sol";
2022-11-looksrare/contracts/proxies/LooksRareProxy.sol::5 => import {IERC721} from "../../contracts/interfaces/IERC721.sol";
2022-11-looksrare/contracts/proxies/LooksRareProxy.sol::6 => import {IERC1155} from "../../contracts/interfaces/IERC1155.sol";

2022-11-looksrare/contracts/proxies/SeaportProxy.sol::7 => import {ItemType, OrderType} from "../libraries/seaport/ConsiderationEnums.sol";
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::8 => import {AdvancedOrder, CriteriaResolver, OrderParameters, OfferItem, ConsiderationItem, FulfillmentComponent, AdditionalRecipient} from "../libraries/seaport/ConsiderationStructs.sol";
2022-11-looksrare/contracts/proxies/SeaportProxy.sol::10 => import {SeaportInterface} from "../interfaces/SeaportInterface.sol";
2022-11-looksrare/scripts/foundry/Deployment.s.sol::5 => import {ERC20EnabledLooksRareAggregator} from "../contracts/ERC20EnabledLooksRareAggregator.sol";
2022-11-looksrare/scripts/foundry/Deployment.s.sol::6 => import {LooksRareAggregator} from "../contracts/LooksRareAggregator.sol";
2022-11-looksrare/scripts/foundry/Deployment.s.sol::7 => import {LooksRareProxy} from "../contracts/proxies/LooksRareProxy.sol";
2022-11-looksrare/scripts/foundry/Deployment.s.sol::8 => import {SeaportProxy} from "../contracts/proxies/SeaportProxy.sol";

```
#### Tools used
Manual




===============================================================================================

### Title
Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;

#### Findings:
```
2022-11-looksrare/contracts/libraries/seaport/ConsiderationEnums.sol::12 => // 2: no partial fills, only offerer or zone can execute
2022-11-looksrare/contracts/libraries/seaport/ConsiderationEnums.sol::27 => // 2: no partial fills, only offerer or zone can execute
2022-11-looksrare/contracts/libraries/seaport/ConsiderationEnums.sol::33 => // 4: no partial fills, anyone can execute
2022-11-looksrare/contracts/libraries/seaport/ConsiderationEnums.sol::45 => // 8: no partial fills, anyone can execute
2022-11-looksrare/contracts/libraries/seaport/ConsiderationEnums.sol::81 => // 20: no partial fills, anyone can execute
2022-11-looksrare/contracts/libraries/seaport/ConsiderationEnums.sol::84 => // 21: partial fills supported, anyone can execute
2022-11-looksrare/contracts/libraries/seaport/ConsiderationEnums.sol::87 => // 22: no partial fills, only offerer or zone can execute
2022-11-looksrare/contracts/libraries/seaport/ConsiderationEnums.sol::90 => // 23: partial fills supported, only offerer or zone can execute
2022-11-looksrare/contracts/libraries/seaport/ConsiderationEnums.sol::102 => // 2: provide ERC20 item to receive offered ERC721 item.
2022-11-looksrare/contracts/libraries/seaport/ConsiderationEnums.sol::108 => // 4: provide ERC721 item to receive offered ERC20 item.
2022-11-looksrare/contracts/libraries/seaport/ConsiderationEnums.sol::123 => // 2: ERC721 items
2022-11-looksrare/contracts/libraries/seaport/ConsiderationEnums.sol::129 => // 4: ERC721 items where a number of tokenIds are supported
```
#### Tools used
Manual

