# NC
## Use open pragma on interfaces

- On [`ISignatureChecker.sol#L2`](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/ISignatureChecker.sol#L2) its using open pragma from version `^0.8.0` but custom errors are supported since version `0.8.4` so the open pragma should be `^0.8.4`. I recommend to change it to `pragma solidity ^0.8.4;`

- On [`IERC20EnabledLooksRareAggregator.sol#L2`](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC20EnabledLooksRareAggregator.sol#L2) its using a fixed pragma `pragma solidity 0.8.17;`
But it should have an open pragma and it should start from `0.8.4` because you use custom errors, i recommend to change it to
`pragma solidity ^0.8.4;`


- On [`IProxy.sol#L2`](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IProxy.sol#L2) its using a fixed pragma `pragma solidity 0.8.17;`
But it should have an open pragma and it should start from `0.8.4` because you use custom errors, i recommend to change it to
`pragma solidity ^0.8.4;`

- On [`OrderStructs.sol#L2`](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/libraries/OrderStructs.sol#L2) its using a fixed pragma `pragma solidity 0.8.17;` i recommend to change it to
`pragma solidity ^0.8.0;`

- On [`OrderEnums.sol#L2`](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/libraries/OrderEnums.sol#L2) its using a fixed pragma `pragma solidity 0.8.17;` i recommend to change it to
`pragma solidity ^0.8.0;`

- On [`ConsiderationEnums.sol#L2`](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/libraries/seaport/ConsiderationEnums.sol#L2) its using a fixed pragma `pragma solidity 0.8.17;` i recommend to change it to
`pragma solidity ^0.8.0;`

- On [`ConsiderationStructs.sol#L2`](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/libraries/seaport/ConsiderationStructs.sol#L2) its using a fixed pragma `pragma solidity 0.8.17;` i recommend to change it to
`pragma solidity ^0.8.0;`


