# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1     | Immutable state variables lack zero address checks | Low | 5 |
| 2     | Use scientific notation | NC | 3 |


## Findings

### 1- Immutable state variables lack zero address checks  :

Constructors should check the values written in an immutable state variables(address) are not the zero value (address(0))

#### Risk : Low 

#### Proof of Concept

Instances include:

File: contracts/ERC20EnabledLooksRareAggregator.sol [Line 22](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L22)
```
aggregator = ILooksRareAggregator(_aggregator);
```

File: contracts/proxies/SeaportProxy.sol [Line 46](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L46)
```
marketplace = SeaportInterface(_marketplace);
```

File: contracts/proxies/SeaportProxy.sol [Line 47](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L47)
```
aggregator = _aggregator;
```

File: contracts/proxies/LooksRareProxy.sol [Line 38](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L38)
```
marketplace = ILooksRareExchange(_marketplace);
```

File: contracts/proxies/LooksRareProxy.sol [Line 39](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L39)
```
aggregator = _aggregator;
```

#### Mitigation
Add non-zero address checks in the constructors for the instances aforementioned.

### 5- Use scientific notation :

When using multiples of 10 you shouldn't use decimal literals or exponentiation (e.g. 1000000, 10**18) but use scientific notation (e.g. 1e6) for better readability.

#### Risk : NON CRITICAL

#### Proof of Concept

Instances include:

File: contracts/LooksRareAggregator.sol [Line 158](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L158)
```
if (bp > 10000) revert FeeTooHigh();
```

File: contracts/proxies/SeaportProxy.sol [Line 147](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L147)
```
uint256 orderFee = (orders[i].price * feeBp) / 10000;
```

File: contracts/proxies/SeaportProxy.sol [Line 207](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L207)
```
uint256 orderFee = (price * feeBp) / 10000;
```

#### Mitigation

Use scientific notation for the instances aforementioned.