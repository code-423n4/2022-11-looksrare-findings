## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Unused `receive()` Function Will Lock Ether In Contract  | 2 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Missing Contract-existence Checks Before Low-level Calls | 7 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Missing parameter validation in `constructor` | 5 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Out of bound loop block gas limit | 1 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Can't remove old proxy fee data | 1 |

Total: 15 contexts over 5 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Critical Changes Should Use Two-step Procedure | 2 |
| [NC&#x2011;2](#NC&#x2011;2) | Event Is Missing Indexed Fields | 1 |
| [NC&#x2011;3](#NC&#x2011;3) | Implementation contract may not be initialized | 3 |
| [NC&#x2011;4](#NC&#x2011;4) | Avoid Floating Pragmas: The Version Should Be Locked | 10 |
| [NC&#x2011;5](#NC&#x2011;5) | Lines are too long | 2 |
| [NC&#x2011;6](#NC&#x2011;6) | Duplicate imports | 2 |

Total: 20 contexts over 6 issues

## Low Risk Issues



### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Unused `receive()` Function Will Lock Ether In Contract 

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

#### <ins>Proof Of Concept</ins>


```
220: receive() external payable {}
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/LooksRareAggregator.sol#L220

```
86: receive() external payable {}
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/proxies/SeaportProxy.sol#L86



#### <ins>Recommended Mitigation Steps</ins>

The function should call another function, otherwise it should revert



### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address. 

#### <ins>Proof Of Concept</ins>


```
88: (bool success, bytes memory returnData) = singleTradeData.proxy.delegatecall(proxyCalldata);
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/LooksRareAggregator.sol#L88

```
30: (bool status, ) = collection.call(
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L30

```
52: (bool status, ) = collection.call(
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L52

```
25: (bool status, bytes memory data) = currency.call(abi.encodeWithSelector(IERC20.approve.selector, to, amount));
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L25

```
30: (bool status, bytes memory data) = currency.call(
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L30

```
51: (bool status, bytes memory data) = currency.call(abi.encodeWithSelector(IERC20.transfer.selector, to, amount));
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L51

```
27: (bool status, ) = collection.call(abi.encodeWithSelector(IERC721.transferFrom.selector, from, to, tokenId));
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L27




#### <ins>Recommended Mitigation Steps</ins>

In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`



### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Missing parameter validation in `constructor`

Some parameters of constructors are not checked for invalid values.

#### <ins>Proof Of Concept</ins>

```
22: aggregator = ILooksRareAggregator(_aggregator);
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/ERC20EnabledLooksRareAggregator.sol#L22

```
38: marketplace = ILooksRareExchange(_marketplace);
39: aggregator = _aggregator;
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/proxies/LooksRareProxy.sol#L38-L39

```
46: marketplace = SeaportInterface(_marketplace);
47: aggregator = _aggregator;
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/proxies/SeaportProxy.sol#L46-L47



#### <ins>Recommended Mitigation Steps</ins>

Validate the parameters.


### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Out of bound loop block gas limit

`tradeData` arrays that are provided in the `execute` functions in `LooksRareAggregator.sol` may exceed block gas limit if provided with a very large `tradeData` list.

#### <ins>Proof Of Concept</ins>
```
for (uint256 i; i < tradeDataLength; ) {
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L69

#### Recommended Mitigation Steps
Consider introducing max limits on the `tradeData` that can be sent in the `execution` function.


### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Can't remove old proxy fee data

It is not possible to remove old proxy fee data should it accumlate a large mapping of proxy fee data.

#### <ins>Proof Of Concept</ins>

```
mapping(address => FeeData) private _proxyFeeData;
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L46

#### Recommended Mitigation Steps

Add a function to delete old `_proxyFeeData`

## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

Specifically `setERC20EnabledLooksRareAggregator` can only be set once as described, it is important that it is set correctly.

#### <ins>Proof Of Concept</ins>

```
120: function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external onlyOwner {
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/LooksRareAggregator.sol#L120

```
153: function setFee(
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/LooksRareAggregator.sol#L153



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Event Is Missing Indexed Fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). 

Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

#### <ins>Proof Of Concept</ins>


```
event FeeUpdated(address proxy, uint256 bp, address recipient);
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/interfaces/ILooksRareAggregator.sol#L44






### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```
constructor(address _aggregator) {
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/ERC20EnabledLooksRareAggregator.sol#L21

```
constructor(address _marketplace, address _aggregator) {
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/proxies/LooksRareProxy.sol#L37

```
constructor(address _marketplace, address _aggregator) {
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/proxies/SeaportProxy.sol#L45





### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Avoid Floating Pragmas: The Version Should Be Locked

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### <ins>Proof Of Concept</ins>

```
Found usage of floating pragmas ^0.8.14 of Solidity in [OwnableTwoSteps.sol]
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/OwnableTwoSteps.sol#L2

```
Found usage of floating pragmas ^0.8.14 of Solidity in [ReentrancyGuard.sol]
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/ReentrancyGuard.sol#L2

```
Found usage of floating pragmas ^0.8.14 of Solidity in [SignatureChecker.sol]
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/SignatureChecker.sol#L2

```
Found usage of floating pragmas ^0.8.0 of Solidity in [IERC20.sol]
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/interfaces/IERC20.sol#L2

```
Found usage of floating pragmas ^0.8.0 of Solidity in [IERC721.sol]
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/interfaces/IERC721.sol#L2

```
Found usage of floating pragmas ^0.8.14 of Solidity in [LowLevelERC1155Transfer.sol]
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L2

```
Found usage of floating pragmas ^0.8.14 of Solidity in [LowLevelERC20Approve.sol]
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L2

```
Found usage of floating pragmas ^0.8.14 of Solidity in [LowLevelERC20Transfer.sol]
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L4

```
Found usage of floating pragmas ^0.8.14 of Solidity in [LowLevelERC721Transfer.sol]
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L2

```
Found usage of floating pragmas ^0.8.14 of Solidity in [LowLevelETH.sol]
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/lowLevelCallers/LowLevelETH.sol#L2



### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length
Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

#### <ins>Proof Of Concept</ins>

```
35: // An arbitrary 32-byte value that will be supplied to the zone when fulfilling restricted orders that the zone can utilize when making a determination on whether to authorize the order
```

https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/proxies/SeaportProxy.sol#L35


### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Duplicate imports

The contract `LookRareAggregator.sol` contains multiple duplicate imports

#### <ins>Proof Of Concept</ins>
File: LooksRareAggregator.sol

```
9: import {LooksRareProxy} from "./proxies/LooksRareProxy.sol";
15: import {LooksRareProxy} from "./proxies/LooksRareProxy.sol";
```

```
11: import {TokenRescuer} from "./TokenRescuer.sol";
17: import {TokenRescuer} from "./TokenRescuer.sol";
```

```
12: import {TokenReceiver} from "./TokenReceiver.sol";
16: import {TokenReceiver} from "./TokenReceiver.sol";
```

```
10: import {BasicOrder, TokenTransfer} from "./libraries/OrderStructs.sol";
14: import {BasicOrder, FeeData, TokenTransfer} from "./libraries/OrderStructs.sol";
```