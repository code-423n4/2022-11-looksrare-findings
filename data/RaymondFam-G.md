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


