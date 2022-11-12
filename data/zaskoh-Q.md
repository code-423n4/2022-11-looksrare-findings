# Solidity pragma versioning

Solidity pragma versioning should be exactly same in all contracts. Currently there is a mix in versions.
All Contracts and Interfaces can have **pragma solidity 0.8.17;**

Change:
```bash
File: contracts/interfaces/IERC20.sol
2: pragma solidity ^0.8.0;

File: contracts/interfaces/IERC721.sol
2: pragma solidity ^0.8.0;

File: contracts/interfaces/IERC1155.sol
2: pragma solidity ^0.8.0;

File: contracts/interfaces/IERC1271.sol
2: pragma solidity ^0.8.0;

File: contracts/interfaces/IOwnableTwoSteps.sol
2: pragma solidity ^0.8.14;

File: contracts/interfaces/IReentrancyGuard.sol
2: pragma solidity ^0.8.0;

File: contracts/interfaces/ISignatureChecker.sol
2: pragma solidity ^0.8.0;

File: contracts/interfaces/IWETH.sol
2: pragma solidity >=0.5.0;

File: contracts/lowLevelCallers/LowLevelERC20Approve.sol
2: pragma solidity ^0.8.14;

File: contracts/lowLevelCallers/LowLevelERC20Transfer.sol
4: pragma solidity ^0.8.14;

File: contracts/lowLevelCallers/LowLevelERC721Transfer.sol
2: pragma solidity ^0.8.14;

File: contracts/lowLevelCallers/LowLevelERC1155Transfer.sol
2: pragma solidity ^0.8.14;

File: contracts/lowLevelCallers/LowLevelETH.sol
2: pragma solidity ^0.8.14;

File: contracts/OwnableTwoSteps.sol
2: pragma solidity ^0.8.14;

File: contracts/ReentrancyGuard.sol
2: pragma solidity ^0.8.14;

File: contracts/SignatureChecker.sol
2: pragma solidity ^0.8.14;
```

# erc20EnabledLooksRareAggregator in contracts/LooksRareAggregator.sol is not set in constructor
As the ERC20EnabledLooksRareAggregator is using the address of LooksRareAggregator as an immutable variable it is not possible to first deploy the ERC20EnabledLooksRareAggregator.
This also means, that ERC20EnabledLooksRareAggregator is only usable as soon as LooksRareAggregator opened the address via setERC20EnabledLooksRareAggregator.
Until that time the function will always revert if tokenTransfersLength is > 0.

# _setupDelayForRenouncingOwnership not implemented in constructor as suggested in comment
```bash
File: contracts/OwnableTwoSteps.sol
122:      * @dev This function is expected to be included in the constructor of the contract that inherits this contract.
123:      *      If it is not set, there is no timelock to renounce the ownership.
```

The comment suggest to implement this function in the constructor of the contracts that inherits.  
contracts/TokenRescuer.sol doesn't implement the function in it's constructor, so the timelock is always 0 here.

# Unnecessary Imports
Some contracts have unnecessary imports that are never used and decrease readability

```bash
File: contracts/LooksRareAggregator.sol
09: import {LooksRareProxy} from "./proxies/LooksRareProxy.sol"; //  unnecessary import => imported on Line 15 again
10: import {BasicOrder, TokenTransfer} from "./libraries/OrderStructs.sol"; //  unnecessary import => imported on Line 14 again
11: import {TokenRescuer} from "./TokenRescuer.sol"; //  unnecessary import => imported on Line 17 again
12: import {TokenReceiver} from "./TokenReceiver.sol"; // unnecessary import => imported on Line 16 again
14: import {BasicOrder, FeeData, TokenTransfer} from "./libraries/OrderStructs.sol"; // unnecessary import BasicOrder

File: contracts/lowLevelCallers/LowLevelETH.sol
4: import {IWETH} from "../interfaces/IWETH.sol"; // unnecessary import 

File: contracts/proxies/LooksRareProxy.sol
05: import {IERC721} from "../../contracts/interfaces/IERC721.sol"; // unnecessary import
06: import {IERC1155} from "../../contracts/interfaces/IERC1155.sol"; // unnecessary import
11: import {BasicOrder, FeeData} from "../libraries/OrderStructs.sol"; // unnecessary import FeeData

File: contracts/proxies/SeaportProxy.sol
4: import {IERC20} from "../interfaces/IERC20.sol"; // unnecessary import
5: import {CollectionType} from "../libraries/OrderEnums.sol"; // unnecessary import
6: import {BasicOrder, FeeData} from "../libraries/OrderStructs.sol"; // unnecessary import FeeData

```

# Add missing documentation

The contracts are very well documented and clear to read. Nearly every function and contract have complete natspec comments.
Only a few are missing and could be added for a better overview.
```bash
File: contracts/ERC20EnabledLooksRareAggregator.sol
39:     function _pullERC20Tokens(TokenTransfer[] calldata tokenTransfers, address source) private {
// missing comments for function

File: contracts/TokenReceiver.sol
4: abstract contract TokenReceiver {
// missing comments for all functions

File: contracts/proxies/LooksRareProxy.sol
107:     function _executeSingleOrder(
// missing comments for function

File: contracts/proxies/SeaportProxy.sol
088:     function _executeAtomicOrders(
136:     function _handleFees(
166:     function _transferFee(
178:     function _executeNonAtomicOrders(
227:     function _populateParameters(BasicOrder calldata order, OrderExtraData memory orderExtraData)
// missing comments for function

File: contracts/SignatureChecker.sol
51:     /**
52:      * @notice Recover the signer of a signature (for EOA)
53:      * @param hash Hash of the signed message
54:      * @param signature Bytes containing the signature (64 or 65 bytes)
55:      */
56:     function _recoverEOASigner(bytes32 hash, bytes memory signature) internal pure returns (address signer) {
// missing @return address signer comment

File: contracts/LooksRareAggregator.sol
148:     /**
149:      * @param proxy Proxy to apply the fee to
150:      * @param bp Fee basis point
151:      * @param recipient Fee recipient
152:      */
153:     function setFee(
// missing @notice

File: contracts/LooksRareAggregator.sol
179:     /**
180:      * @param proxy The marketplace proxy's address
181:      * @param selector The marketplace proxy's function selector
182:      * @return Whether the marketplace proxy's function can be called from the aggregator
183:      */
184:     function supportsProxyFunction(address proxy, bytes4 selector) external view returns (bool) {
// missing @notice

File: contracts/LooksRareAggregator.sol
222:     function _encodeCalldataAndValidateFeeBp(
241:     function _returnERC20TokensIfAny(TokenTransfer[] calldata tokenTransfers, address recipient) private {
// missing comments for function

File: contracts/lowLevelCallers/LowLevelETH.sol
40:     /**
41:      * @notice Return ETH back to the designated sender if any ETH is left in the payable call.
42:      */
// missing @param recipient
```
