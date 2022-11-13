## Duplicate imports in LooksRareAggregator contract

- BasicOrder
- TokenTransfer
- LooksRareProxy
- TokenReceiver
- TokenRescuer

## Unused imports in LooksRareAggregator contract

- BasicOrder
- LooksRareProxy

## Unused imports in LooksRareProxy contract

- IERC721
- IERC1155
- FeeData

## Unused imports in SeaPortProxy contract

- IERC20
- CollectionType
- FeeData

## `value` member in TradeData struct isn't used in the codebase

The comment associated to this field reads "The amount of ETH passed to the proxy during the function call", however proxies are delegatecalled, there's no need to send ETH. This is likely a field that was used at certain point in the codebase .
