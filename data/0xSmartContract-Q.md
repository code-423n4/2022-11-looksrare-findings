## Summary
### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| The length of the array arguments in the `execute` function is not checked | 1 |
|[L-02]| Every token accidentally sent to the Contract cannot be recovered | 1 |
|[L-03]| Remove unused code | 1 |
|[L-04]| Some ERC20 tokens should need to approve(0) first| 1 |
|[L-05]|Insufficient coverage | 1 |
|[L-06]|  `_transferETH` function create dirty bits | 1 |
|[L-07]|Critical Address Changes Should Use Two-step Procedure | 2 |
|[L-08]| Owner can renounce Ownership | 6 |
|[L-09]|Need Fuzzing test| 10 |
|[L-10]| Lock pragmas  to specific compiler version| 1 |
|[L-11]| Loss of precision due to rounding | 1 |
|[L-12]|The transferERC1155 function has a reentrancy vulnerability| 1 |

Total 12 issues
### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [NC-01 ]|Add `I` prefix to interface contracts |1|
| [NC-02] |Not using the latest version of solmate from dependencies  |1|
| [NC-03] | `0 address` check | 3 |
| [NC-04] | Omissions in Events | 1 |
| [NC-05] | Add parameter to Event-Emit | 1|
| [NC-06] |Include ``return parameters`` in _NatSpec comments_  | 1 |
| [NC-07] | Use a more recent version of Solidity | 15 |
| [NC-08] |Solidity compiler optimizations can be problematic | 1 |
| [NC-09] | NatSpec is missing | 7 |
| [NC-10] | Unnecessary receive() | 1  |
| [NC-11] |Signature Malleability of EVM's ecrecover() | 1 |
| [NC-12] |Lines are too long | 3 |
| [NC-13] |Stop using v != 27 && v != 28 or v == 27  v == 28 | 1 |
| [NC-14] |Use a more recent version of  Seaport dependencies | 1 |
| [NC-15] |`Empty blocks` should be _removed_ or _Emit_ something | 1 |
| [NC-16] |Use Underscores for Number Literals | 3 |

Total 16 issues
### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Generate perfect code headers every time |

Total 1 suggestion
### [L-01] The length of the array arguments in the `execute` function is not checked


```solidity
contracts/ERC20EnabledLooksRareAggregator.sol:
  28:     function execute(
  29:         TokenTransfer[] calldata tokenTransfers,
  30:         ILooksRareAggregator.TradeData[] calldata tradeData,
  31:         address recipient,
  32:         bool isAtomic
  33:     ) external payable {
  34:         if (tokenTransfers.length == 0) revert UseLooksRareAggregatorDirectly();
  35:         _pullERC20Tokens(tokenTransfers, msg.sender);
  36:         aggregator.execute{value: msg.value}(tokenTransfers, tradeData, msg.sender, recipient, isAtomic);
  37:     }

```


The `tokenTransfers` and `tradeData` arguments are not checked for the same length, this can cause serious security issues

## Recommended Mitigation Steps

```solidity

 function execute(
        TokenTransfer[] calldata tokenTransfers,
        ILooksRareAggregator.TradeData[] calldata tradeData,
        address recipient,
        bool isAtomic
    ) external payable {
        if (tokenTransfers.length == 0) revert UseLooksRareAggregatorDirectly();
+     if (tokenTransfers.length != tradeData.length) revert ShouldSameLength();

        _pullERC20Tokens(tokenTransfers, msg.sender);
        aggregator.execute{value: msg.value}(tokenTransfers, tradeData, msg.sender, recipient, isAtomic);
    }
```
### [L-02] Every token accidentally sent to the Contract cannot be recovered 

Owner has the possibility to save the token to be recovered in , but the risk remains due to the possibility of the owner to renounceOwnership

```solidity

contracts/TokenRescuer.sol:
  33       */
  34:     function rescueERC20(address currency, address to) external onlyOwner {
  35:         uint256 withdrawAmount = IERC20(currency).balanceOf(address(this)) - 1;
  36:         if (withdrawAmount == 0) revert InsufficientAmount();
  37:         _executeERC20DirectTransfer(currency, to, withdrawAmount);
  38:     }
  39  }

```

Proof of Concept

1-Alice is the onlyOwner person of the contract and has renounceOwnership with the authority in the Ownable contract
2-Bob sends 10,000-USDT to the contract due to an incorrect copy-paste
3-This amount can never be recovered and is stuck in the contract forever


Recommended Mitigation Steps:

Disable the `confirmOwnershipRenouncement`  property, this also avoids the risk that other `onlyOwner` functions will not run forever.

### [L-03] Remove unused code

This code is not used in the project, remove it;

```solidity
contracts/OwnableTwoSteps.sol:
  142       */
  143:     function _setupDelayForRenouncingOwnership(uint256 _delay) internal {
  144:         delay = _delay;
  145:     }
  146  }
```
### [L-04] Some ERC20 tokens should need to approve(0) first

Some tokens (like USDT) do not work when changing the allowance from an existing non-zero allowance value. They must first be approved by zero and then the actual allowance must be approved.


```solidity

contracts/LooksRareAggregator.sol:
  170       */
  171:     function approve(
  172:         address marketplace,
  173:         address currency,
  174:         uint256 amount
  175:     ) external onlyOwner {
  176:         _executeERC20Approve(currency, marketplace, amount);
  177:     }


 function _executeERC20Approve(
        address currency,
        address to,
        uint256 amount
    ) internal {
        (bool status, bytes memory data) = currency.call(abi.encodeWithSelector(IERC20.approve.selector, to, amount));

        if (!status) revert ERC20ApprovalFail();
        if (data.length > 0) {
            if (!abi.decode(data, (bool))) revert ERC20ApprovalFail();
        }
    }

```

When using one of these unsupported tokens, all transactions revert and the protocol cannot be used.

Recommended Mitigation Steps
approve with a zero amount first before setting the actual amount.

```diff
+          IERC20(_token).approve(router, 0);
            IERC20(_token).approve(router, _amount);
```

### [L-05] Insufficient coverage

**Description:**
Testing all functions is best practice in terms of security criteria.

```js

+-------------------------------------------------------+------------------+-------------------+------------------+-----------------+
| File                                                  | % Lines          | % Statements      | % Branches       | % Funcs         |
+===================================================================================================================================+
| contracts/OwnableTwoSteps.sol                         | 96.43% (27/28)   | 97.14% (34/35)    | 100.00% (18/18)  | 83.33% (5/6)    |
|-------------------------------------------------------+------------------+-------------------+------------------+-----------------|
| contracts/TokenReceiver.sol                           | 66.67% (2/3)     | 66.67% (2/3)      | 100.00% (0/0)    | 66.67% (2/3)    |
|-------------------------------------------------------+------------------+-------------------+------------------+-----------------|
| contracts/prototype/V0Aggregator.sol                  | 83.33% (15/18)   | 77.27% (17/22)    | 25.00% (2/8)     | 66.67% (2/3)    |
|-------------------------------------------------------+------------------+-------------------+------------------+-----------------|
| Total                                                 | 37.64% (341/906) | 38.33% (399/1041) | 73.83% (110/149) | 58.40% (73/125) |
+-------------------------------------------------------+------------------+-------------------+------------------+-----------------+

```

Due to its capacity, test coverage is expected to be 100%
### [L-06]  `_transferETH` function create dirty bits

This explanation should be added in the NatSpec comments of this function that sends ether with call;

Note that this code probably isn’t secure or a good use case for assembly because a lot of memory management and security checks are bypassed.
Use with caution! Some functions in this contract knowingly create dirty bits at the destination of the free memory pointer.


```solidity

contracts/lowLevelCallers/LowLevelETH.sol:
  18       */
  19:     function _transferETH(address _to, uint256 _amount) internal {
  20:         bool status;
  21: 
  22:         assembly {
  23:             status := call(gas(), _to, _amount, 0, 0, 0, 0)
  24:         }
  25: 
  26:         if (!status) revert ETHTransferFail();
  27:     }
```

Recommendation:
Add this comment to `_transferETH`function;
/// @dev Use with caution! Some functions in this contract knowingly create dirty bits at the destination of the free memory pointer. Note that this code probably isn’t secure or a good use case for assembly because a lot of memory management and security checks are bypassed.


### [L-07] Critical Address Changes Should Use Two-step Procedure

The critical procedures should be two step process.
See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure


```solidity
contracts/LooksRareAggregator.sol:

120:     function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external onlyOwner {

153:      function setFee(address proxy, uint256 bp,address recipient)

```

Recommended Mitigation Steps
Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

### [L-08] Owner can renounce Ownership

**Context:**
[OwnableTwoSteps.sol#L110-L117](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L110-L117)

**Description:**
Typically, the contract’s owner is the account that deploys the contract. As a result, the owner is able to perform certain privileged activities.

The LooksRare Ownable used in this project contract implements renounceOwnership. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.


`onlyOwner` functions;
```js
contracts/LooksRareAggregator.sol:
  119      // Enable making ERC20 trades by setting the ERC20 enabled LooksRare aggregator
  120:     function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external onlyOwner
  131      // Enable calling the specified proxy's trade function
  132:     function addFunction(address proxy, bytes4 selector) external onlyOwner {

  142       // Disable calling the specified proxy's trade function
  143:     function removeFunction(address proxy, bytes4 selector) external onlyOwner {

  155       // Proxy to apply the fee to
  156       function setFee() external onlyOwner {

  173       // Approve marketplaces to transfer ERC20 tokens from the aggregator
  174           function approve( address marketplace,address currency,uint256 amount) external onlyOwner {


  198        // Rescue any of the contract's trapped ERC721 tokens
  199:      function rescueERC721() external onlyOwner {


```

**Recommendation:**
We recommend to either reimplement the function to disable it or to clearly specify if it is part of the contract design.

### [L-09] Need Fuzzing test

**Context:**
Project uncheckeds list:

```js
10 results - 4 files

contracts/ERC20EnabledLooksRareAggregator.sol:
  48: unchecked {

contracts/LooksRareAggregator.sol:
   82: unchecked {
  103: unchecked {
  247: unchecked {

contracts/proxies/LooksRareProxy.sol:
  101: unchecked {

contracts/proxies/SeaportProxy.sol: 
  111: unchecked {
  128: unchecked {
  158: unchecked {
  219: unchecked {
  264: unchecked {

```

**Description:**
In total 4 contracts, 10 unchecked are used, the functions used are critical. For this reason, there must be fuzzing tests in the tests of the project. Not seen in tests.

**Recommendation:**
Use should fuzzing test like Echidna.

As Alberto Cuesta Canada said:
_Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now._

https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05

### [L-10] _Lock pragmas_ to specific compiler version

**Description:**
Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally. 
https://swcregistry.io/docs/SWC-103

**Recommendation:**
Ethereum Smart Contract Best Practices - Lock pragmas to specific compiler version.
[solidity-specific/locking-pragmas](https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/)

### [L-11] Loss of precision due to rounding

Due to `/10000`, users can avoid paying fee if `price * feeBp` result is below 10000

```solidity
contracts/proxies/SeaportProxy.sol:
  206                  if (feeRecipient != address(0)) {
  207:                     uint256 orderFee = (price * feeBp) / 10000;
  208                      if (currency == lastOrderCurrency) {

```

**Recommendation:**
A lower limit can be added to the price and/or feeBp values

### [L-12] The transferERC1155 function has a reentrancy vulnerability



The `rescueERC1155` function is used to recover stuck ERC1155 tokens in the contract, and it does this with an internal function `_executeERC1155SafeBatchTransferFrom` ;

```solidity
contracts/LooksRareAggregator.sol:
  210       */
  211:     function rescueERC1155(
  212:         address collection,
  213:         address to,
  214:         uint256[] calldata tokenIds,
  215:         uint256[] calldata amounts
  216:     ) external onlyOwner {
  217:         _executeERC1155SafeBatchTransferFrom(collection, address(this), to, tokenIds, amounts);
  218:     }
```


The `_executeERC1155SafeBatchTransferFrom` function also executes the `safeBatchTransferFrom` function of the ERC1155 contract ; 

```solidity

 function _executeERC1155SafeBatchTransferFrom(
        address collection,
        address from,
        address to,
        uint256[] calldata tokenIds,
        uint256[] calldata amounts
    ) internal {
        (bool status, ) = collection.call(
            abi.encodeWithSelector(IERC1155.safeBatchTransferFrom.selector, from, to, tokenIds, amounts, "")
        );

        if (!status) revert ERC1155SafeBatchTransferFrom();
    }
```


ERC1155 in `safeBatchTransferFrom` function has `checkOnERC1155Received` and gives re-entry scope to the contract to which this NFT was sent,

Therefore Re-entry protection should be added to the rescueERC1155 function



For a similar vulnerability;
https://blocksecteam.medium.com/revest-finance-vulnerabilities-more-than-re-entrancy-1609957b742f. ($2 million lost)

https://medium.com/siren/siren-incident-report-264e57f16d7  ($3.5 million lost)



How does _checkOnERC1155Received work ? ;

_checkOnERC1155Received has the following behavior :
1- if the receiving address to is an EOA, it returns true
2- if the receiving address to is a smart contract, it will call on the contract the onERC1155Received method seen above and check the result :
3 -if the contract returns the magic value, _checkOnERC1155Received returns true
4- else it returns false and the _safeTransfer call fails (as the require condition is not met).

### [NC-01] Add `I` prefix to interface contracts
[SeaportInterface.sol#L28](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/SeaportInterface.sol#L28)


Filename prefixed by I usually indicates an interface rather than a contract.

Therefore, the filename of SeaportInterface.sol shoul be `ISeaportInterface.sol` 

should  be prefixed by `I`
### [NC-02] Not using the latest version of solmate from dependencies

The package.json configuration file says that the project is using 6.6.1 of Solmate which has a not last update version


```js
package.json:
  84    },
  85:   "dependencies": {
  87:     "solmate": "^6.6.1"
  88    }

```

**Recommendation:**
Use patched versions

### [NC-03] `0 address` check

0 address control should be done in these parts;

```js
3 results - 3 files

contracts/ERC20EnabledLooksRareAggregator.sol:
  20       */
  21:     constructor(address _aggregator) {
  22          aggregator = ILooksRareAggregator(_aggregator);


contracts/proxies/LooksRareProxy.sol:
  36       */
  37:     constructor(address _marketplace, address _aggregator) {
  38          marketplace = ILooksRareExchange(_marketplace);

contracts/proxies/SeaportProxy.sol:
  44       */
  45:     constructor(address _marketplace, address _aggregator) {
  46          marketplace = SeaportInterface(_marketplace);

```


**Recommendation:**
Add code like this;
`if (oracle == address(0)) revert ADDRESS_ZERO();`
### [NC-04] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:

Events with no old value;

```solidity

contracts/LooksRareAggregator.sol:
  152       */
  153:     function setFee(
  154:         address proxy,
  155:         uint256 bp,
  156:         address recipient
  157:     ) external onlyOwner {
  158:         if (bp > 10000) revert FeeTooHigh();
  159:         _proxyFeeData[proxy].bp = bp;
  160:         _proxyFeeData[proxy].recipient = recipient;
  161: 
  162:         emit FeeUpdated(proxy, bp, recipient);
  163:     }

```

### [NC-05] Add parameter to Event-Emit

Some event-emit description hasn’t parameter. Add to parameter  for front-end website or client app , they can has that something has happened on the blockchain.

Events with no old value;

```solidity

2 results - 2 files

contracts/LooksRareAggregator.sol:
  123:         emit ERC20EnabledLooksRareAggregatorSet();

contracts/OwnableTwoSteps.sol:
   64:         emit CancelOwnershipTransfer();

```
### [NC-06] Include ``return parameters`` in _NatSpec comments_

**Context:**

```solidity
contracts/SignatureChecker.sol:
  54       * @param signature Bytes containing the signature (64 or 65 bytes)
  55:      */
  56:     function _recoverEOASigner(bytes32 hash, bytes memory signature) internal pure returns (address signer) {

```


https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

If Return parameters are declared, you must prefix them with "/// @return".

Some code analysis programs do analysis by reading NatSpec details, if they can't see the "@return" tag, they do incomplete analysis.

**Recommendation:**
Include return parameters in NatSpec comments

Recommendation Code Style:
 ```js
    /// @notice information about what a function does
    /// @param pageId The id of the page to get the URI for.
    /// @return Returns a page's URI if it has been minted 
    function tokenURI(uint256 pageId) public view virtual override returns (string memory) {
        if (pageId == 0 || pageId > currentId) revert("NOT_MINTED");

        return string.concat(BASE_URI, pageId.toString());
    }
```
### [NC-07] Use a more recent version of Solidity (15 instances)

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest Solidity version.
For the security fix list in the versions;
https://github.com/ethereum/solidity/blob/develop/Changelog.md

Use of ^0.8.14 with vulnerability related to "Optimizer Error Regarding Memory Side Effects of Inline Compiling"

ref : https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/

Solidity version 0.8.14 is used in the project, some contracts, it is recommended to use 0.8.17 due to the above vulnerability.


**Recommendation:**
Old version of Solidity is used `(^0.8.14)`, newer version can be used `(0.8.17)`

### [NC-08] Solidity compiler optimizations can be problematic


```js
hardhat.config.ts:
  33    },
  34:   solidity: {
  35:     compilers: [
  36:       {
  37:         version: "0.8.17",
  38:         settings: { optimizer: { enabled: true, runs: 888888 }, viaIR: true },
  39:       },
  40:     ],
  41:   },

```

**Description:**
Protocol has enabled optional compiler optimizations in Solidity.
There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. 

Therefore, it is unclear how well they are being tested and exercised.
High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. 

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported.
A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe.
It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario
A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.



**Recommendation:**
Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.
Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.


### [NC-09] NatSpec is missing 

**Description:**
NatSpec is missing for the following functions , constructor and modifier:

```solidity
7 results

contracts/LooksRareAggregator.sol:
  220:     receive() external payable {}

  222:     function _encodeCalldataAndValidateFeeBp(

  241:     function _returnERC20TokensIfAny(TokenTransfer[] calldata tokenTransfers, address recipient) private {


contracts/ERC20EnabledLooksRareAggregator.sol:
  
  39:     function _pullERC20Tokens(TokenTransfer[] calldata tokenTransfers, address source) private {



contracts/TokenReceiver.sol:

   5:     function onERC721Received(

  14:     function onERC1155Received(

  24:     function onERC1155BatchReceived(

```

### [NC-10] Unnecessary receive()

**Description:**
There doesn't seem to be a use case for the existence of the receive() function. In fact, I will recommend removing it as it will prevent accidental native token transfers to the contract.

```solidity
contracts/LooksRareAggregator.sol:

  220:     receive() external payable {}
```

### [NC-11] Signature Malleability of EVM's ecrecover()

```solidity
contracts/SignatureChecker.sol:

60:         signer = ecrecover(hash, v, r, s);

```
**Description:**
Description: The function calls the Solidity ecrecover() function directly to verify the given signatures. However, the ecrecover() EVM opcode allows malleable (non-unique) signatures and thus is susceptible to replay attacks.

Although a replay attack seems not possible for this contract, I recommend using the battle-tested OpenZeppelin's ECDSA library.

*Recommendation:**
Use the ecrecover function from OpenZeppelin's ECDSA library for signature verification. (Ensure using a version > 4.7.3 for there was a critical bug >= 4.1.0 < 4.7.3).

### [NC-12] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length
Reference:
https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length


[OwnableTwoSteps.sol#L122](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L122)
[OwnableTwoSteps.sol#L8-L9](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L8-L9)
[IERC1155.sol#L5](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/IERC1155.sol#L5)

### [NC-13] Stop using v != 27 && v != 28 or v == 27 || v == 28

```solidity
contracts/SignatureChecker.sol:

  48:         if (v != 27 && v != 28) revert BadSignatureV(v);
```

See this for reference ;
https://twitter.com/alexberegszaszi/status/1534461421454606336?s=20&t=H0Dv3ZT2bicx00hLWJk7Fg

### [NC-14] Use a more recent version of  Seaport dependencies


**Description:**
For security, it is best practice to use the latest Seaport version.


```js
package.json:
  42    },
  43:   "devDependencies": {
  51:     "@opensea/seaport-js": "^1.0.6",
```

For the security fix list in the versions;
https://www.npmjs.com/package/@opensea/seaport-js/v/1.0.8

**Recommendation:**
Old version is used `(1.0.6)`, newer version can be used `(1.0.8)`

### [NC-15] `Empty blocks` should be _removed_ or _Emit_ something


**Description:**
Code contains empty block

```js
2 results - 2 files

contracts/LooksRareAggregator.sol:
  220:     receive() external payable {}

contracts/proxies/SeaportProxy.sol:
  86:     receive() external payable {}

```

**Recommendation:**
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


### [NC-16] Use Underscores for Number Literals

There are multiple occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.


```js
3 results - 2 files

contracts/LooksRareAggregator.sol:
  158:         if (bp > 10000) revert FeeTooHigh();

contracts/proxies/SeaportProxy.sol:
  147:  uint256 orderFee = (orders[i].price * feeBp) / 10000;

  207:  uint256 orderFee = (price * feeBp) / 10000;
```


Recommendation: 
Consider using underscores for number literals to improve its readability.

```diff
-   if (bp > 10000) revert FeeTooHigh();
+  if (bp > 10_000) revert FeeTooHigh();

```

### [S-01] Generate perfect code headers every time

**Description:**
I recommend using header for Solidity code layout and readability

https://github.com/transmissions11/headers

```js
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```
