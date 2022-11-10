**IProxy**
- L9 - The ZeroAddres() error is created but it is never used in the two contracts that implement IProxy.


**SeaportInterface**
- L28 - As a matter of convention, it is much more coherent to call it ISeaport.sol


**ConsiderationStructs**
- L7/10/16 - In the creation of the OrderType enum, only the FULL_RESTRICTED value is used throughout the project. All the others are not used, therefore they have no reason to exist.

- L22/25/31/34/37/40/43/46/49/52/55/58/61/64/67/70/73/76/79/82/85/88/91 - At creation from the enum BasicOrderType only the value ETH_TO_ERC721_FULL_RESTRICTED is used throughout the project. All the others are not used, therefore they have no reason to exist.

- L95/137 - The enum BasicOrderRouteType and Side are created, but they are never used. Therefore they should be removed.

- L127/130/133 - The ERC1155, ERC721_WITH_CRITERIA and ERC1155_WITH_CRITERIA states are not used in the creation of the ItemType enums. All the others are not used, therefore they have no reason to exist.


**LooksRareProxy**
- L37/38/39 - In the constructor several variables are set in storage (marketplace and aggregator) that are immutable, therefore it should be validated that the entered address is not zero, since it would make multiple functions unusable (DoS).

- L5/6/11 - The interfaces IERC721, IERC1155 and the FeeData library are imported, but they are never used, therefore they should be removed.


**SeaportProxy**
- L4/6 - The IERC20 interface and the FeeData library are imported, but they are never used, therefore they should be removed.

- L45/46/47 - In the constructor several variables are set in storage (marketplace and aggregator) that are immutable, therefore it should be validated that the entered address is not zero, since it would make multiple functions unusable (DoS).

- L166 - The _trasferFee() function first passes the fee parameter, then lastOrderCurrency and recipient, this order is somewhat unintuitive when using it, since all transfers begin with the sender, receiver and lastly, amount.


**ERC20EnabledLooksRareAggregator**
- L22 - In the constructor the variable is set in storage aggregator that are immutable, therefore it should be validated that the entered address is not zero, since it would make multiple functions unusable (DoS).


**LooksRareAggregator**
- L9/15 - The LooksRareProxy contract is imported twice, but they are never used, so they should be removed.

- L10/14 - The BasicOrder contract is imported twice, but they are never used, therefore they should be eliminated. TokenTransfer is used, but is imported twice.

- L120 - In the function setERC20EnabledLooksRareAggregator() it is validated that the parameter is not set to zero, but the variable has a deploy value of 0, therefore at the time of deploy it is in an incorrect state, therefore it could create a constructor to set this variable.


**TokenTransferrer**
- L16/17 - In the _transferTokenToRecipient() function, the input recipient is passed first and then the collection, this seems incorrect to me since the fact that the recipient is passed first and then the contract is counter-intuitive. Being unintuitive can cause confusion for users and developers.

