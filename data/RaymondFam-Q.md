## Un-indexed Parameters in Events
Consider indexing parameters for events, serving as logs filter when looking for specifically wanted data. Up to three parameters in an event function can receive the attribute `indexed` which will cause the respective arguments to be treated as log topics instead of data. Here is one of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol#L44

```
    event FeeUpdated(address proxy, uint256 bp, address recipient);
```
## Descriptive and Verbose Options
Consider making the naming of local variables more verbose and descriptive so all other peer developers would better be able to comprehend the intended statement logic, significantly enhancing the code readability. Here is one of the instances entailed:

https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/ILooksRareAggregator.sol#L44

```
    @ bp
    event FeeUpdated(address proxy, uint256 bp, address recipient);
```
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/OrderStructs.sol#L25

```
    @ bp
    uint256 bp; // Aggregator fee basis point
```

## Typo Mistakes
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/seaport/ConsiderationStructs.sol#L168

```
    @ are
 *      type is restricted and the offerer or zone are not the caller.
```



