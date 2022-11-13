# Summary
## Low
1. L01 - Missing checks for address(0x0) when assigning values to address state variables
2. L02 - Unused/empty receive()/fallback() function
## Non critical
3. NC01 - Floating pragma and Outdated compiler version
4. NC02 - Use constant instead of magic number

# Low
## L01 - Missing checks for address(0x0) when assigning values to address state variables

### Mitigation
Add check for address(0x0)

### Lines in the code
[SeaportProxy.sol#L47](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/SeaportProxy.sol#L47)
[LooksRareProxy.sol#L39](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L39)

## L02 - Unused/empty receive()/fallback() function
### Description
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))). 
Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds

### Lines in the code
[LooksRareAggregator.sol#L220](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L220)


# Non Critical
## NC01 - Floating pragma and Outdated compiler version
### Description
There are some contract with the pragma solidity directive ^0.8.14 or ^0.8.0. It is recommended to specify a fixed compiler version to ensure that the bytecode produced 
does not vary between builds. 
This is especially important if you rely on bytecode-level verification of the code.

### Mitigation
Lock the pragma.

### Lines in the code
[ReentrancyGuard.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/ReentrancyGuard.sol#L2)
[OwnableTwoSteps.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L2)
[SignatureChecker.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/SignatureChecker.sol#L2)
[LowLevelERC20Approve.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L2)
[LowLevelERC20Transfer.sol#L4](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L4)
[LowLevelERC721Transfer.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L2)
[LowLevelERC1155Transfer.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L2)
[LowLevelETH.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelETH.sol#L2)
[IERC20.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC20.sol#L2)
[IERC721.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC721.sol#L2)
[IERC1155.sol#L2](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IERC1155.sol#L2)

## NC02 - Use constant instead of magic number
### Description
Replace the magic numbers for a constant that describe the meaning of it.

### Lines in the code
[LooksRareAggregator.sol#L158](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L158)
