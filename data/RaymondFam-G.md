## calldata and memory
When running a function we could pass the function parameters as calldata or memory for variables such as strings, bytes, structs, arrays etc. If we are not modifying the passed parameter, we should pass it as calldata because calldata is more gas efficient than memory.

Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. However, it alleviates the compiler from the `abi.decode()` step that copies each index of the calldata to the memory index, bypassing the cost of 60 gas on each iteration. Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L108-L109

```
        OrderTypes.TakerOrder memory takerBid,
        OrderTypes.MakerOrder memory makerAsk,
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L227

```
    function _populateParameters(BasicOrder calldata order, OrderExtraData memory orderExtraData)
```
## Use Storage Instead of Memory for Structs/Arrays
A storage pointer is cheaper since copying a state struct in memory would incur as many SLOADs and MSTOREs as there are slots. In another words, this causes all fields of the struct/array to be read from storage, incurring a Gcoldsload (2100 gas) for each field of the struct/array, and then further incurring an additional MLOAD rather than a cheap stack read. As such, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables will be much cheaper, involving only Gcoldsload for all associated field reads. Read the whole struct/array into a memory variable only when it is being returned by the function, passed into a function that requires memory, or if the array/struct is being read from another memory array/struct. Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L65-L69

```
            BasicOrder memory order = orders[i];

            OrderExtraData memory orderExtraData = abi.decode(ordersExtraData[i], (OrderExtraData));

            OrderTypes.MakerOrder memory makerAsk;
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L90

```
            OrderTypes.TakerOrder memory takerBid;
```
All other instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L97-L103
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L188-L189
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L244-L254

## += and -= Costs More Gas
`+=` generally costs 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop. As an example, the following line of code could be rewritten as:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L150

```
                fee = fee + orderFee;
```
All other instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L109
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L209

## Unchecked SafeMath Saves Gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, utilize `unchecked { ... }` in a wider scope to use the previous wrapping behavior. For instance, the for loop instance below may be refactored as follows to include `+=`:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L102-L114

```
    unchecked {
        for (uint256 i; i < ordersLength; ++i;) {
            OrderExtraData memory orderExtraData = abi.decode(ordersExtraData[i], (OrderExtraData));
            advancedOrders[i].parameters = _populateParameters(orders[i], orderExtraData);
            advancedOrders[i].numerator = orderExtraData.numerator;
            advancedOrders[i].denominator = orderExtraData.denominator;
            advancedOrders[i].signature = orders[i].signature;

            if (orders[i].currency == address(0)) ethValue += orders[i].price;         
        }
    }
```
All other instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L145-L161
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L187-L222

## Payable Access Control Functions Costs Less Gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`. Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenRescuer.sol#L22

```
    function rescueETH(address to) external onlyOwner {
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenRescuer.sol#L34

```
    function rescueERC20(address currency, address to) external onlyOwner {
```
All other instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L120
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L132
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L143
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L157
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L175
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L199
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L216

## Function Order Affects Gas Consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the Optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
  solidity: {
    version: "0.8.17",
    settings: {
      optimizer: {
        enabled: true,
        runs: 1000,
      },
    },
  },
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI

Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## Private/Internal Function Embedded Modifier Reduces Contract Size
Consider having the logic of a modifier embedded through a private (doesn't matter whether or not the contract entails any child contracts since the private visibility saves even more gas on function calls than the internal visibility) function to reduce contract size if need be. For instance, the following instance of modifier may be rewritten as follows:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L30-L35

```
    function _onlyOwner() private view {
        if (msg.sender != owner) {
            revert NotOwner();
        }
    }

    modifier onlyOwner() {
        _onlyOwner();
        _;
    }
```
All other instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ReentrancyGuard.sol#L21-L27

## State Variables Repeatedly Read Should be Cached
SLOADs cost 100 gas each after the 1st one whereas MLOADs/MSTOREs only incur 3 gas each. As such, storage values read multiple times should be cached in the stack memory the first time (costing only 1 SLOAD) and then re-read from this cache to avoid multiple SLOADs.

For instance, `_proxyFeeData[proxy]` could  be cached in the instance below:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L159-L160

```
        FeeData _feeData = _proxyFeeData[proxy];
        _feeData.bp = bp;
        _feeData.recipient = recipient;
```
## Use of Named Returns for Local Variables Saves Gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable. As an example, the following instance of code block can refactored as follows:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L184-L186

```
    function supportsProxyFunction(address proxy, bytes4 selector) external view returns (bool status) {
        status = _proxyFunctionSelectors[proxy][selector];
    }
```