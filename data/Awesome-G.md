## Uncheck arithmetics operations that can’t underflow/overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible, some gas can be saved by using an unchecked block.
There are 3 instances of this issue:

[Line 102-114](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L102-L114), [Line 145-161](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L145-L161), [Line 187-222](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L187-L222)

here is an example of how you can refactor it:

[Line 102-114](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenRescuer.sol#L22)

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

## Functions With Access Control Cheaper if Payable

A function with access control marked as payable will be cheaper for legitimate callers: the compiler removes checks for `msg.value`, saving approximately `20` gas per function call.

There are 9 instances of this issue:
[Line 22](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenRescuer.sol#L22), [Line 34](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenRescuer.sol#L34), [Line 120](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L120), [Line 132](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L132), [Line 143](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L143), [Line 157](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L157), [Line 175](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L175), [Line 199](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L199), [Line 216](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L216)

Here is an example of how you can refactor it:

[Line 22](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenRescuer.sol#L22)

```
Line 22:    function rescueETH(address to) external onlyOwner {
```

[Line 34](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenRescuer.sol#L34)

```
Line 34:    function rescueERC20(address currency, address to) external onlyOwner {
```

## Remove Shorthand Addition/Subtraction Assignment

Instead of using the shorthand of addition/subtraction assignment operators (`+=`, `-=`)
it costs less to remove the shorthand (`x += y` same as `x = x+y`) saves ~22 gas
There are 3 instances of this issue:
[Line 150](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L150), [Line 109](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L109), [Line 209](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L102-L114)

## Store Data in `calldata` Instead of `Memory` for Certain Function Parameters

Instead of copying variables to memory, it is typically more cost-effective to load them immediately from `calldata`. If all you need to do is read data, you can conserve gas by saving the data in `calldata`.
There are 2 instances of this issue:

[Line 108-109](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L108-L109)

```
Line 108:    OrderTypes.TakerOrder memory takerBid,
Line 109:    OrderTypes.MakerOrder memory makerAsk,
```

[Line 227](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L227)

```
Line 227:    function _populateParameters(BasicOrder calldata order, OrderExtraData memory orderExtraData)
```

## Caching `Storage` Variables in `Memory` To Save Gas

Anytime you are reading from `storage` more than once, it is cheaper in gas cost to cache the variable in `memory`: a `SLOAD` cost 100gas, while `MLOAD` and `MSTORE` cost 3 gas.

Gas savings: at least 97 gas.

There is 1 instance of this issue:
[Line 159-160](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L159-L160)

## Using `Storage` Instead of `Memory` for Structs/Arrays Saves Gas

When fetching data from a `storage` location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from `storage`, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declaring the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incurring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.

There are 3 instances of this issue:

[Line 65-69](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L65-L69), [Line 90](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L90)

## Function Order Affects Gas Consumption

public/external function names and public member variable names can be optimized to save gas. See [this](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, per sorted position shifted


