# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Variables inside `struct` should be packed to save gas  | 1    |
| 2      | `storage` pointer to a `struct` is cheaper that copying each value of the struct into `memory`, same apply for array and mapping  |  1 |

## Findings

### 1- Variables inside `struct` should be packed to save gas :

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings.

There is 1 instance of this issue:

File: contracts/libraries/OrderStructs.sol [Line 14-15](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/libraries/OrderStructs.sol#L14-L15)

```
struct BasicOrder {
  address signer; // The order's maker
  address collection; // The address of the ERC721/ERC1155 token to be purchased
  CollectionType collectionType; // 0 for ERC721, 1 for ERC1155
  uint256[] tokenIds; // The IDs of the tokens to be purchased
  uint256[] amounts; // Always 1 when ERC721, can be > 1 if ERC1155
  uint256 price; // The *taker bid* price to pay for the order
  address currency; // The order's currency, address(0) for ETH
  uint256 startTime; // The timestamp when the order starts becoming valid
  uint256 endTime; // The timestamp when the order stops becoming valid
  bytes signature; // split to v,r,s for LooksRare
}
```

As the variables `startTime` and `endTime` can't really overflow 2^64, their values should be packed to save gas, the struct should be modified as follow  :

```
struct BasicOrder {
  address signer; // The order's maker
  address collection; // The address of the ERC721/ERC1155 token to be purchased
  CollectionType collectionType; // 0 for ERC721, 1 for ERC1155
  uint256[] tokenIds; // The IDs of the tokens to be purchased
  uint256[] amounts; // Always 1 when ERC721, can be > 1 if ERC1155
  uint256 price; // The *taker bid* price to pay for the order
  address currency; // The order's currency, address(0) for ETH
  uint64 startTime; // The timestamp when the order starts becoming valid
  uint64 endTime; // The timestamp when the order stops becoming valid
  bytes signature; // split to v,r,s for LooksRare
}
```

### 2- `storage` pointer to a `struct` is cheaper that copying each value of the struct into `memory`, same apply for array and mapping :

It may not be obvious, but every time you copy a `storage` struct/array/mapping to a `memory` variable, you are copying each member by reading it from `storage`, which is expensive. And when you use the `storage` keyword, you are just storing a pointer to the `storage`, which is much cheaper. Exception: case when you need to read all or many members multiple times. In report included only cases that saved gas.

There is 1 instance of this issue:

File: contracts/LooksRareAggregator.sol [Line 227](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L227)
```
FeeData memory feeData = _proxyFeeData[singleTradeData.proxy];
```