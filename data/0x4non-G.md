# Gas Optimizations

Declare your time variables as "uint40" rather than "uint256", to save gas in structs and contract storage.
Source: https://twitter.com/PaulRBerg/status/1591832937179250693

On lines:
https://github.com/code-423n4/2022-11-looksrare/blob/f4c90ca149f4aeeac125605a56166297b717201a/contracts/libraries/seaport/ConsiderationStructs.sol#L28-L29
https://github.com/code-423n4/2022-11-looksrare/blob/f4c90ca149f4aeeac125605a56166297b717201a/contracts/libraries/seaport/ConsiderationStructs.sol#L49-L50
https://github.com/code-423n4/2022-11-looksrare/blob/f4c90ca149f4aeeac125605a56166297b717201a/contracts/libraries/seaport/ConsiderationStructs.sol#L62-L63
https://github.com/code-423n4/2022-11-looksrare/blob/f4c90ca149f4aeeac125605a56166297b717201a/contracts/libraries/seaport/ConsiderationStructs.sol#L111-L112
https://github.com/code-423n4/2022-11-looksrare/blob/f4c90ca149f4aeeac125605a56166297b717201a/contracts/libraries/seaport/ConsiderationStructs.sol#L145-L146
https://github.com/code-423n4/2022-11-looksrare/blob/f4c90ca149f4aeeac125605a56166297b717201a/contracts/libraries/OrderStructs.sol#L14-L15