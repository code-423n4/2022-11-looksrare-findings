QA1. Lock the most recent version of solidity 0.8.17consistently (currrently, some use 0.8.14, some use 0.8.17).

QA2. 
https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/SeaportProxy.sol#L88
https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/SeaportProxy.sol#L178

There is no need to implement the function ``_executeAtomicOrders`` separately, instead one can easily add a revert statement into the ``catch{}`` part in ``_executeNonAtomicOrders`` to support the atomic case, in which we need to revert the whole transaction when the execution of one order fails as follows:
```
try{
}
catch{
     if(isAtomic) revert ExecuteAtomicOrdersFailure();
}

```
This will save much audit effort and code duplication. As a result, the fucntion ``_handleFees`` can be eliminated as well.

QA3: Simply the function ``_executeSingleOrder()`` as follows
```
  function _executeSingleOrder(
        OrderTypes.TakerOrder memory takerBid,
        OrderTypes.MakerOrder memory makerAsk,
        address recipient,
        CollectionType collectionType,
        bool isAtomic
    ) private
 {
            try marketplace.matchAskWithTakerBidUsingETHAndWETH{value: takerBid.price}(takerBid, makerAsk) {
                _transferTokenToRecipient(
                    collectionType,
                    recipient,
                    makerAsk.collection,
                    makerAsk.tokenId,
                    makerAsk.amount
                );
            } catch {
                  if(isAtomic) revert ExecuteSingleOrderFailure();
            }
}
```

QA3: https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/ERC20EnabledLooksRareAggregator.sol#L21
It will be helpful to make the contract pausible in case the owner of the ``LooksRareAggregator`` contract is compromised. 

QA3: the ``LooksRareAggregator`` gives all power to ONE owner, although the owner could be a mult-sig contract, the management is still too centralized and risky.  It is recommended to have a balance-and-check mechanism: for example, 1) ``setERC20EnabledLooksRareAggregator`` is manged by one role; 2) the rescue functions are managed by a second role; and 3) those that are related to business, such as *setFee()* and market setup by a third role. 

QA4: ``ERC20EnabledLooksRareAggregator`` needs a whitelist mechanism for ERC20 tokens to avoid people use fake tokens to exchange for good tokens. One way of doing that is right before ``_pullERC20Tokens(tokenTransfers, msg.sender);``, call a function ``isWhiteListed(tokenTransfers)`` in ``LooksRareAggregator`` to verify. 

QA5: the low level transfer functions at
https://github.com/code-423n4/2022-11-looksrare/tree/main/contracts/lowLevelCallers
have not checked if the token address is a valid smart contract. If there is no code at the target address, all transfers will still return a success return even though no operation has been performed. 

QA6: 