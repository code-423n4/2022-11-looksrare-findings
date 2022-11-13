# Report


## QA Report


| |Issue|Instances|
|-|:-|:-:|
| [Low-1] | Multiple Pragma(Solidity version) used | 16 |
| [Low-2] | Absence of Zero address check, while setting critical and immutable state variable | 5 |
| [Low-3] | Consider Adding checks for Signature Malleability | 1 |
| [Low-4] | There always 1 token(That can be ETH or Any ERC20) remain stucked inside contract due to code logic of "rescueETH()","rescueERC20()" functions | 2 |
| [Low-5] | Token (ETH and ERC20) may be lost | 2 |


| |Issue|Instances|
|-|:-|:-:|
| [NC-01] | Same Import Statement written multiple times | 4 |
| [NC-02] | Contract don't have any checks for return data value from low level call "call()" | 3 |


### [LOW-01] Multiple Pragma(Solidity version) used

Below  all contracts use Solidity version 0.8.17

```solidity
File: contracts/ERC20EnabledLooksRareAggregator.sol
File: contracts/LooksRareAggregator.sol
File: contracts/TokenReceiver.sol
File: contracts/TokenRescuer.sol
File: contracts/TokenTransferrer.sol
File: contracts/proxies/LooksRareProxy.sol
File: contracts/proxies/SeaportProxy.sol
File: contracts/libraries/seaport/ConsiderationStructs.sol
File: contracts/libraries/seaport/ConsiderationEnums.sol
File: contracts/libraries/OrderEnums.sol
File: contracts/libraries/OrderStructs.sol
File: contracts/interfaces/IERC20EnabledLooksRareAggregator.sol
File: contracts/interfaces/IGemSwap.sol
File: contracts/interfaces/ILooksRareAggregator.sol
File: contracts/interfaces/IProxy.sol
File: contracts/interfaces/SeaportInterface.sol

```

These contracts use Solidity version ^0.8.14;

```solidity
File: contracts/OwnableTwoSteps.sol
File: contracts/ReentrancyGuard.sol
File: contracts/SignatureChecker.sol

File: contracts/lowLevelCallers/LowLevelERC1155Transfer.sol
File: contracts/lowLevelCallers/LowLevelERC20Approve.sol
File: contracts/lowLevelCallers/LowLevelERC20Transfer.sol
File: contracts/lowLevelCallers/LowLevelERC721Transfer.sol
File: contracts/lowLevelCallers/LowLevelETH.sol
File: contracts/interfaces/IOwnableTwoSteps.sol

```

These contracts using ^0.8.0;

```solidity
File: contracts/interfaces/IERC20.sol
File: contracts/interfaces/IERC1155.sol
File: contracts/interfaces/IERC721.sol
File: contracts/interfaces/IERC1271.sol
File: contracts/interfaces/IReentrancyGuard.sol
File: contracts/interfaces/ISignatureChecker.sol

```

Its using >=0.5.0

```solidity
File: contracts/interfaces/IWETH.sol

```

### [LOW-02] Absence of Zero address check, while setting critical and immutable state variable

*Instances (5)*:
```solidity
File: contracts/ERC20EnabledLooksRareAggregator.sol

22:         aggregator = ILooksRareAggregator(_aggregator);

```
```solidity
File: contracts/proxies/LooksRareProxy.sol

38:         marketplace = ILooksRareExchange(_marketplace);
39:         aggregator = _aggregator;

```
```solidity
File: contracts/proxies/SeaportProxy.sol

46:         marketplace = SeaportInterface(_marketplace);
47:         aggregator = _aggregator;

```



### [LOW-03] Consider Adding checks for Signature Malleability

Use OpenZeppelin’s ECDSA contract rather than calling ecrecover() directly

*Instances (1)*:
```solidity

File: contracts/SignatureChecker.sol
60:         signer = ecrecover(hash, v, r, s);

```


### [LOW-04] There always 1 token(That can be ETH or Any ERC20) remain stucked inside contract due to code logic of "rescueETH()","rescueERC20()" functions

*Instances (1)*:
```solidity

File: contracts/TokenRescuer.sol
In function rescueETH()
23:         uint256 withdrawAmount = address(this).balance - 1; 

In function rescueERC20()
35:          uint256 withdrawAmount = IERC20(currency).balanceOf(address(this)) - 1;

```

### [LOW-05] Token (ETH and ERC20) may be lost
There is absence of zero address check for receiver address i.e "to" parameter in functions "rescueETH()"&"rescueERC20()". 

*Instances (1)*:
```solidity

File: contracts/TokenRescuer.sol
In function rescueETH()
22-25:       function rescueETH(address to) external onlyOwner {

In function rescueERC20()
34-38:       function rescueERC20(address currency, address to) external onlyOwner { 

```





### [NC-01] Same Import Statement written multiple times
*Instances (4)*:
```solidity
File: contracts/LooksRareAggregator.sol

14:         import {BasicOrder, FeeData, TokenTransfer} from "./libraries/OrderStructs.sol";  
15:         import {LooksRareProxy} from "./proxies/LooksRareProxy.sol";  
16:         import {TokenReceiver} from "./TokenReceiver.sol";  
17:         import {TokenRescuer} from "./TokenRescuer.sol";

```



### [NC-02] Contract don't have any checks for return data value from low level call "call()"
*Instances (3)*:
```solidity
File: contracts/lowLevelCallers/LowLevelERC721Transfer.sol

27:         (bool status, ) = collection.call(abi.encodeWithSelector(IERC721.transferFrom.selector, from, to, tokenId)); 

```
```solidity
File: contracts/lowLevelCallers/LowLevelERC1155Transfer.sol

30-32:         (bool status, ) = collection.call(
                    abi.encodeWithSelector(IERC1155.safeTransferFrom.selector, from, to, tokenId, amount, "")
                                              ); 
52-54:          (bool status, ) = collection.call(
                     abi.encodeWithSelector(IERC1155.safeBatchTransferFrom.selector, from, to, tokenIds, amounts, "")
                     );

```
