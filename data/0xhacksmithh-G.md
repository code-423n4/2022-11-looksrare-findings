# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1] | Function that guaranteed to revert when called by normal user can be marked payable  | 9 |
| [GAS-2] | <x> += <y> costs more gas than <x> = <x> + <y> for state variables | 3 |
| [GAS-3] | Using bools for storage incurs overhead | 2 |

### [GAS-01] Function that guaranteed to revert when called by normal user can be marked payable 
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. 

*Instances (9)*:

```solidity
File: contracts/LooksRareAggregator.sol

120:         function setERC20EnabledLooksRareAggregator(address _erc20EnabledLooksRareAggregator) external onlyOwner {
132:         function addFunction(address proxy, bytes4 selector) external onlyOwner {
143:         function removeFunction(address proxy, bytes4 selector) external onlyOwner {
153-157    function setFee(
                 address proxy,
                 uint256 bp,
                 address recipient
                 ) external onlyOwner {
171-175       function approve(
                    address marketplace,
                    address currency,
                    uint256 amount
                    ) external onlyOwner {
195-199     function rescueERC721(
                   address collection,
                   address to,
                   uint256 tokenId
                   ) external onlyOwner {
211-216     function rescueERC1155(
                     address collection,
                     address to,
                     uint256[] calldata tokenIds,
                     uint256[] calldata amounts
                     ) external onlyOwner {
```
```solidity

File: contracts/TokenRescuer.sol
In function rescueETH()
22-25:             function rescueETH(address to) external onlyOwner {

In function rescueERC20()
34-38:             function rescueERC20(address currency, address to) external onlyOwner { 

```

### [GAS-02] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

*Instances (3)*:
```solidity
File: contracts/proxies/SeaportProxy.sol

109:        if (orders[i].currency == address(0)) ethValue += orders[i].price;
150:        fee += orderFee;
209:        fee += orderFee;


```



### [GAS-3] Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

*Instances (2)*:

```solidity
File: contracts/libraries/seaport/ConsiderationStructs.sol

186:         bool isValidated;
187:         bool isCancelled;

```





