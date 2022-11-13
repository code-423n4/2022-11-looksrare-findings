## Table of Contents
- Internal Function Called Only Once can be Inlined
- Use Calldata instead of Memory for Read Only Function Parameters
- Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas
- Empty Blocks Should Emit Something or be Removed

&ensp;
### Internal Function Called Only Once Can be Inlined

#### Issue
Certain function is defined even though it is called only once.
Inline it instead to where it is called to avoid usage of extra gas.

#### PoC
Total of 8 instances found.

1. _executeERC1155SafeTransferFrom() of LowLevelERC1155Transfer.sol
This function is never called. Delete it since it is never used.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L23

2. _executeERC1155SafeBatchTransferFrom() of LowLevelERC1155Transfer.sol
This function called only once at line 217 of LooksRareAggregator.sol.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L45
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L217

3. _returnETHIfAnyWithOneWeiLeft() of LowLevelETH.sol
This function is never called. Delete it since it is never used.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelETH.sol#L54

4. _executeERC20TransferFrom() of LowLevelERC20Transfer.sol
This function called only once at line 42 of ERC20EnabledLooksRareAggregator.sol.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L24
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/ERC20EnabledLooksRareAggregator.sol#L42

5. _executeERC721TransferFrom() of LowLevelERC721Transfer.sol
This function called only once at line 200 of LooksRareAggregator.sol.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L21
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L200

6. _recoverEOASigner() of SignatureChecker.sol
This function called only once at line 78.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/SignatureChecker.sol#L56
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/SignatureChecker.sol#L78

7. _setupDelayForRenouncingOwnership() of OwnableTwoSteps.sol
This function is never called. Delete it since it is never used.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L125

8. _executeERC20Approve() of LowLevelERC20Approve.sol
This function called only once at line 176 of LooksRareAggregator.sol.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L20
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L176

#### Mitigation
I recommend to not define above functions and instead inline it at place it is called.

&ensp;
### Use Calldata instead of Memory for Read Only Function Parameters

#### Issue
It is cheaper gas to use calldata than memory if the function parameter is read only.
Calldata is a non-modifiable, non-persistent area where function arguments are stored, 
and behaves mostly like memory. More details on following link.
link: https://docs.soliditylang.org/en/v0.8.15/types.html#data-location

#### PoC
Total of 5 instances found.
```
./TokenReceiver.sol:19:        bytes memory
./TokenReceiver.sol:27:        uint256[] memory,
./TokenReceiver.sol:28:        uint256[] memory,
./TokenReceiver.sol:29:        bytes memory
./LooksRareProxy.sol:53:        bytes memory,
```

#### Mitigation
Change memory to calldata

&ensp;
### Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas

#### Issue
Since EVM operates on 32 bytes at a time, if the element is smaller than that, the EVM must use more operations
in order to reduce the elements from 32 bytes to specified size. Therefore it is more gas saving to use 32 bytes 
unless it is used in a struct or state variable in order to reduce storage slot. 

Reference: https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html

#### PoC
Total of 1 instance found.
```
./SignatureChecker.sol:25:            uint8 v
```

#### Mitigation
I suggest using uint256 instead of anything smaller and downcast where needed.

&ensp;
### Empty Blocks Should Emit Something or be Removed

#### Issue
There are function with empty blocks. These should do something like emit an event or reverting.
If not it should be removed from the contract.

#### PoC
Total of 2 instances found.
```solidity
./LooksRareAggregator.sol:220:    receive() external payable {}
./SeaportProxy.sol:86:    receive() external payable {}
```

#### Mitigation
Add emit or revert in the function block.

&ensp;