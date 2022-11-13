## `x = x + y` is more efficient than `x += y` (same for subtracting)

The amount of gas saved can be sizeable when repeated in a loop.

For example:

File: `SeaportProxy.sol` [Line 145-161](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L145-L161)

```
150                fee += orderFee;
```

Instead, you can change the code to the following:

```
150                fee = fee + orderFee;
```

Here are some more instances of this issue:

File: `SeaportProxy.sol` [Line 109](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L109)
File: `SeaportProxy.sol` [Line 209](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L209)

## Math should be unchecked if overflowing is not a possibility

Consider the following example:

File: `SeaportProxy.sol` [Line 145-161](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L145-L161)

```
145        for (uint256 i; i < ordersLength; ) {
146            address currency = orders[i].currency;
147            uint256 orderFee = (orders[i].price * feeBp) / 10000;
148
149            if (currency == lastOrderCurrency) {
150                fee += orderFee;
151            } else {
152                if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);
153
154                lastOrderCurrency = currency;
155                fee = orderFee;
156            }
157
158            unchecked {
159                ++i;
160            }
161        }
```

Since we know that `fee` (Line 150) will not overflow, we can uncheck math and save the gas.

Instead, you can rewrite the code to the following:

```
    unchecked {
        for (uint256 i; i < ordersLength; ++i) {
            address currency = orders[i].currency;
            uint256 orderFee = (orders[i].price * feeBp) / 10000;

            if (currency == lastOrderCurrency) {
                fee += orderFee;
            } else {
                if (fee > 0) _transferFee(fee, lastOrderCurrency, feeRecipient);

                lastOrderCurrency = currency;
                fee = orderFee;
            }
        }
    }
```

Here are the rest of the instances:

File: `SeaportProxy.sol` [Line 102-114](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L102-L114)
File: `SeaportProxy.sol` [Line 187-222](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L187-L222)

## Use `calldata` instead of `memory`

If you are not going to modify the parameter, you can pass it as `calldata` to save gas (about 60 gas).

For example:

File: `LooksRareProxy.sol` [Line 108-109](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L108-L109)

```
        OrderTypes.TakerOrder memory takerBid,
        OrderTypes.MakerOrder memory makerAsk,
```

Since these parameters aren't going to be modified, they can be passed as calldata instead:

```
        OrderTypes.TakerOrder calldata takerBid,
        OrderTypes.MakerOrder calldata makerAsk,
```

Here is another instance of this issue:

File: `SeaportProxy.sol` [Line 227](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L227)

## Use `storage` instead of `memory` for structs and arrays

`storage` costs less gas since copying a state struct in memory would incur as many SLOADs and MSTOREs as there are slots.

For example:

File: `LooksRareProxy.sol` [Line 90](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L90)

```
            OrderTypes.TakerOrder memory takerBid;
```

Instead of `memory`, you can use `storage`:

```
            OrderTypes.TakerOrder storage takerBid;
```

Here are the rest of the instances:

File: `LooksRareProxy.sol` [Line 65-69](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L65-L69)
File: `SeaportProxy.sol` [Line 97-103](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L97-L103)
File: `SeaportProxy.sol` [Line 188-189](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L188-L189)
File: `SeaportProxy.sol` [Line 244-254](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L244-L254)
