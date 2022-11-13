## G-1 Avoid call `_handleFees` method when `feeBp` parameter is equals to 0.

Currently only receipt fee is checked to call `_handleFees` method but if you see its implementation, `orderFee` value will be 0 if mentioned parameter is equal to 0.

So, it is possible to save all `_handleFees()` execution gas making this validation.

Validation to add:
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L133
`if (feeRecipient != address(0) && feeBp != 0) _handleFees(orders, feeBp, feeRecipient);`

Place where the fees become 0 when `feeBp` is equals to 0:
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L147



----------------------
----------------------
----------------------


## G-2 Set `lastOrderCurrency` variable at the beggining of `_handleFees` method to remove loop condition.

Note: This improvement is valid if you add G-1 too.

Consider that when `handleFees` method is called, `orders.length` is validated to have some row. 
So you could set `lastOrderCurrency = orders[0].currency` and remove `if (fee != 0)` loop condition. Doing that, it is not possible to have `fee == 0` at this point because `feeBP` parameter is not equals to 0 and the first time the loop is executed `if (currency == lastOrderCurrency)` condition will be true.

Place assignation `lastOrderCurrency = orders[0].currency` in the following line:
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L141

Remove conditional and call `_transferFee` method direclty:
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L152

Important: Does not remove the conditional at line 163 because is still valid.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L163



----------------------
----------------------
----------------------


## G-3 Use calldata instead of memory at LooksRareProxy `execute` method.

Replace memory declaration for calldata for `order` variable due to it is only used to read data.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L65

Original:
``` 
╭──────────────────────────────────────────────────────────────┬─────────────────┬────────┬────────┬────────┬─────────╮    
│ contracts/proxies/LooksRareProxy.sol:LooksRareProxy contract ┆                 ┆        ┆        ┆        ┆         │    
╞══════════════════════════════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡    
│ Deployment Cost                                              ┆ Deployment Size ┆        ┆        ┆        ┆         │    
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤    
│ 1683078                                                      ┆ 8635            ┆        ┆        ┆        ┆         │    
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤    
│ Function Name                                                ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │    
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤    
│ execute                                                      ┆ 300453          ┆ 300453 ┆ 300453 ┆ 300453 ┆ 1       │    
╰──────────────────────────────────────────────────────────────┴─────────────────┴────────┴────────┴────────┴─────────╯  
```

Optimized:
``` 
╭──────────────────────────────────────────────────────────────┬─────────────────┬────────┬────────┬────────┬─────────╮
│ contracts/proxies/LooksRareProxy.sol:LooksRareProxy contract ┆                 ┆        ┆        ┆        ┆         │    
╞══════════════════════════════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡    
│ Deployment Cost                                              ┆ Deployment Size ┆        ┆        ┆        ┆         │    
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤    
│ 1644436                                                      ┆ 8442            ┆        ┆        ┆        ┆         │    
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤    
│ Function Name                                                ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │    
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤    
│ execute                                                      ┆ 298845          ┆ 298845 ┆ 298845 ┆ 298845 ┆ 1       │    
╰──────────────────────────────────────────────────────────────┴─────────────────┴────────┴────────┴────────┴─────────╯   
```

As you can see you will save 1608*N Gas, where N is `orders` parameter length.
Deployment cost is 38642 Gas cheaper too.