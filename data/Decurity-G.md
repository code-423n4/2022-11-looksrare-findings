# Gas Optimization Findings

## 1. Use calldata instead of memory to save gas
### Description
LooksRareProxy.sol#64
```
BasicOrder memory order = orders[i]; //@audit use calldata instead of memory to save gas
```

#### Remediation
Use calldata instead of memory  
```
BasicOrder calldata order = orders[i];
```