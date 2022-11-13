### [L-01] Use of ```ecrecover``` is susceptible to signature malleability


#### Impact
The ```ecrecover``` function is used to recover the address from the signature. The built-in EVM precompile ecrecover is susceptible to signature malleability which could lead to replay attacks (references: https://swcregistry.io/docs/SWC-117, https://swcregistry.io/docs/SWC-121 and https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57).

Consider using OpenZeppelin’s ECDSA library (which prevents this malleability) instead of the built-in function.


#### Findings:
```
contracts/SignatureChecker.sol:L60        signer = ecrecover(hash, v, r, s);

```

### [L-02] Avoid floating pragmas for non-library contracts.


#### Impact
While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
contracts/SignatureChecker.sol:L2     pragma solidity ^0.8.14;

contracts/OwnableTwoSteps.sol:L2     pragma solidity ^0.8.14;

contracts/ReentrancyGuard.sol:L2     pragma solidity ^0.8.14;

contracts/lowLevelCallers/LowLevelETH.sol:L2     pragma solidity ^0.8.14;

contracts/lowLevelCallers/LowLevelERC20Transfer.sol:L4     pragma solidity ^0.8.14;

contracts/lowLevelCallers/LowLevelERC1155Transfer.sol:L2     pragma solidity ^0.8.14;

contracts/lowLevelCallers/LowLevelERC721Transfer.sol:L2     pragma solidity ^0.8.14;

contracts/lowLevelCallers/LowLevelERC20Approve.sol:L2     pragma solidity ^0.8.14;

contracts/interfaces/IERC721.sol:L2     pragma solidity ^0.8.0;

contracts/interfaces/IERC1155.sol:L2     pragma solidity ^0.8.0;

contracts/interfaces/IERC20.sol:L2     pragma solidity ^0.8.0;

```

### [L-03] zero-address checks are missing


#### Impact
Zero-address checks are a best practice for input validation of critical address parameters. Accidental use of zero-addresses may result in exceptions, burn fees/tokens, or force redeployment of contracts.

#### Findings:
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/ERC20EnabledLooksRareAggregator.sol#L22
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L38-L39