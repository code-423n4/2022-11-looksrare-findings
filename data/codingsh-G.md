
 ## G002: Cache Array Length Outside of Loop

 ```solidity
 ERC20EnabledLooksRareAggregator.sol::34 => if (tokenTransfers.length == 0) revert UseLooksRareAggregatorDirectly();
 ERC20EnabledLooksRareAggregator.sol::40 => uint256 tokenTransfersLength = tokenTransfers.length;
 LooksRareAggregator.sol::59 => uint256 tradeDataLength = tradeData.length;
 LooksRareAggregator.sol::62 => uint256 tokenTransfersLength = tokenTransfers.length;
 LooksRareAggregator.sol::242 => uint256 tokenTransfersLength = tokenTransfers.length;
 SignatureChecker.sol::9 => * @notice This contract is used to verify signatures for EOAs (with length of both 65 and 64 bytes) and contracts (ERC-1271).
 SignatureChecker.sol::28 => if (signature.length == 64) {
 SignatureChecker.sol::36 => } else if (signature.length == 65) {
 SignatureChecker.sol::43 => revert WrongSignatureLength(signature.length);
 SignatureChecker.sol::77 => if (signer.code.length == 0) {
 interfaces/ISignatureChecker.sol::14 => error WrongSignatureLength(uint256 length);
 libraries/seaport/ConsiderationStructs.sol::120 => // Total length, excluding dynamic array data: 0x264 (580)
 libraries/seaport/ConsiderationStructs.sol::151 => // offer.length                          // 0x160
 prototype/V0Aggregator.sol::31 => uint256 tradeCount = tradeData.length;
 prototype/V0Aggregator.sol::48 => if (returnData.length > 0) {
 proxies/LooksRareProxy.sol::61 => uint256 ordersLength = orders.length;
 proxies/LooksRareProxy.sol::62 => if (ordersLength == 0 || ordersLength != ordersExtraData.length) revert InvalidOrderLength();
 proxies/SeaportProxy.sol::71 => uint256 ordersLength = orders.length;
 proxies/SeaportProxy.sol::72 => if (ordersLength == 0 || ordersLength != ordersExtraData.length) revert InvalidOrderLength();
 proxies/SeaportProxy.sol::96 => uint256 ordersLength = orders.length;
 proxies/SeaportProxy.sol::143 => uint256 ordersLength = orders.length;
 proxies/SeaportProxy.sol::232 => uint256 recipientsLength = orderExtraData.recipients.length;
```
 ## G003:

 ```solidity
 LooksRareAggregator.sol::92 => if (returnData.length > 0) {
 lowLevelCallers/LowLevelERC20Approve.sol::28 => if (data.length > 0) {
 lowLevelCallers/LowLevelERC20Transfer.sol::35 => if (data.length > 0) {
 lowLevelCallers/LowLevelERC20Transfer.sol::54 => if (data.length > 0) {
 prototype/V0Aggregator.sol::48 => if (returnData.length > 0) {
  ```
  
  
  

 ## G007: Long Revert Strings

 ```solidity
 ERC20EnabledLooksRareAggregator.sol::4 => import {LowLevelERC20Transfer} from "./lowLevelCallers/LowLevelERC20Transfer.sol";
 ERC20EnabledLooksRareAggregator.sol::5 => import {IERC20EnabledLooksRareAggregator} from "./interfaces/IERC20EnabledLooksRareAggregator.sol";
 ERC20EnabledLooksRareAggregator.sol::6 => import {ILooksRareAggregator} from "./interfaces/ILooksRareAggregator.sol";
 LooksRareAggregator.sol::5 => import {LowLevelERC20Approve} from "./lowLevelCallers/LowLevelERC20Approve.sol";
 LooksRareAggregator.sol::6 => import {LowLevelERC721Transfer} from "./lowLevelCallers/LowLevelERC721Transfer.sol";
 LooksRareAggregator.sol::7 => import {LowLevelERC1155Transfer} from "./lowLevelCallers/LowLevelERC1155Transfer.sol";
 LooksRareAggregator.sol::13 => import {ILooksRareAggregator} from "./interfaces/ILooksRareAggregator.sol";
 OwnableTwoSteps.sol::4 => import {IOwnableTwoSteps} from "./interfaces/IOwnableTwoSteps.sol";
 ReentrancyGuard.sol::4 => import {IReentrancyGuard} from "./interfaces/IReentrancyGuard.sol";
 SignatureChecker.sol::5 => import {ISignatureChecker} from "./interfaces/ISignatureChecker.sol";
 TokenRescuer.sol::6 => import {LowLevelERC20Transfer} from "./lowLevelCallers/LowLevelERC20Transfer.sol";
 TokenRescuer.sol::7 => import {LowLevelETH} from "./lowLevelCallers/LowLevelETH.sol";
 interfaces/SeaportInterface.sol::15 => } from "../libraries/seaport/ConsiderationStructs.sol";
 proxies/LooksRareProxy.sol::5 => import {IERC721} from "../../contracts/interfaces/IERC721.sol";
 proxies/LooksRareProxy.sol::6 => import {IERC1155} from "../../contracts/interfaces/IERC1155.sol";
 proxies/LooksRareProxy.sol::7 => import {ILooksRareExchange} from "@looksrare/contracts-exchange-v1/contracts/interfaces/ILooksRareExchange.sol";
 proxies/LooksRareProxy.sol::8 => import {OrderTypes} from "@looksrare/contracts-exchange-v1/contracts/libraries/OrderTypes.sol";
 proxies/SeaportProxy.sol::7 => import {ItemType, OrderType} from "../libraries/seaport/ConsiderationEnums.sol";
 proxies/SeaportProxy.sol::8 => import {AdvancedOrder, CriteriaResolver, OrderParameters, OfferItem, ConsiderationItem, FulfillmentComponent, AdditionalRecipient} from "../libraries/seaport/ConsiderationStructs.sol";
 proxies/SeaportProxy.sol::10 => import {SeaportInterface} from "../interfaces/SeaportInterface.sol";
  ```

## G008: Use Shift Right/Left instead of Division/Multiplication if possible

```solidity
 libraries/seaport/ConsiderationEnums.sol::12 => // 2: no partial fills, only offerer or zone can execute
 libraries/seaport/ConsiderationEnums.sol::27 => // 2: no partial fills, only offerer or zone can execute
 libraries/seaport/ConsiderationEnums.sol::33 => // 4: no partial fills, anyone can execute
 libraries/seaport/ConsiderationEnums.sol::45 => // 8: no partial fills, anyone can execute
 libraries/seaport/ConsiderationEnums.sol::81 => // 20: no partial fills, anyone can execute
 libraries/seaport/ConsiderationEnums.sol::84 => // 21: partial fills supported, anyone can execute
 libraries/seaport/ConsiderationEnums.sol::87 => // 22: no partial fills, only offerer or zone can execute
 libraries/seaport/ConsiderationEnums.sol::90 => // 23: partial fills supported, only offerer or zone can execute
 libraries/seaport/ConsiderationEnums.sol::102 => // 2: provide ERC20 item to receive offered ERC721 item.
 libraries/seaport/ConsiderationEnums.sol::108 => // 4: provide ERC721 item to receive offered ERC20 item.
 libraries/seaport/ConsiderationEnums.sol::123 => // 2: ERC721 items
 libraries/seaport/ConsiderationEnums.sol::129 => // 4: ERC721 items where a number of tokenIds are supported
```

 ## L001: Unsafe ERC20 Operation(s)
  
  ```solidity
 LooksRareAggregator.sol::41 => *         token.transferFrom(victim, attacker, amount) inside the proxy within the context of the
```


