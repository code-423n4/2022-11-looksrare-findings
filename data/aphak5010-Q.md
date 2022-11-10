# LooksRare Aggregator Gas Optimization Findings
## Summary
The Gas savings are calculated using the `forge test --gas-report` command.  
The same tests were used that are provided in the contest repository.

| Issue      | Title | File | Instances | Estimated Gas Savings (Deployments) | Estimated Gas Savings (Method calls) |
| ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| G-00      | Use functions instead of modifiers | - | 2 modifiers | not calculated | not calculated |
| G-01      | Don't save orders.length to memory | SeaportProxy.sol | 1 | 800 | 4 on average |

## [G-00] Use functions instead of modifiers
Modifiers make code more elegant and readable but cost more Gas than functions.  
Arguably, the additional Gas cost outweighs the readability benefit.  

There are 2 instances where modifiers are defined.  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/ReentrancyGuard.sol#L21](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/ReentrancyGuard.sol#L21)  

[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/OwnableTwoSteps.sol#L30](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/OwnableTwoSteps.sol#L30)  

## [G-01] Don't save orders.length to memory
[https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/SeaportProxy.sol#L71-L72](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/SeaportProxy.sol#L71-L72)  

Deployment Gas saved: **800**  
Method call Gas saved: **4 on average**

```solidity
-        uint256 ordersLength = orders.length;
-        if (ordersLength == 0 || ordersLength != ordersExtraData.length) revert InvalidOrderLength();
+        if (orders.length == 0 || orders.length != ordersExtraData.length) revert InvalidOrderLength();
```


