# Report
## Gas Optimizations ##
### [G-1]: Use calldata instead of memory
**Context:**

1. ```OrderTypes.TakerOrder memory takerBid,``` [L108](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L108) 
1. ```OrderTypes.MakerOrder memory makerAsk,``` [L109](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L109) 
1. ```function _populateParameters(BasicOrder calldata order, OrderExtraData memory orderExtraData)``` [L227](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L227) (orderExtraData)
1. ```function _splitSignature(bytes memory signature)``` [L19](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L19) 
1. ```function _recoverEOASigner(bytes32 hash, bytes memory signature) internal pure returns (address signer) {``` [L56](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L56) 
1. ```bytes memory signature``` [L75](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L75) 

**Description:**

If a reference type function parameter is read-only, it is recommended to use calldata instead of memory because this provides significant gas savings. Since Solidity v0.6.9, memory and calldata are allowed in all functions regardless of their visibility type (ie external, public, etc).
