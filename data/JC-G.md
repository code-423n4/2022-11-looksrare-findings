
---

## Summary

- G-01 Multiple address mappings can be combined into a single mapping (2 instances)
- G-02 State variables only set in the constructor should be declared immutable (1 instances)
- G-03 <x> += <y> costs more gas than <x> = <x> + <y> for state variables (3 instances)
- G-04 internal functions only called once can be inlined to save gas (8 instances)
- G-05 Empty blocks should be removed or emit something (4 instances)
- G-06 Use calldata instead of memory for function parameters (8 instances)
- G-07 Using calldata instead of memory for read-only arguments in external functions (2 instances)

Total: 28 instances in 7 issues.

---

## G-01 Multiple address mappings can be combined into a single mapping

Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate saves a storage slot for the mapping. 
Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 

Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

Instances (2):

LooksRareAggregator.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L45
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L46



## G-02 State variables only set in the constructor should be declared immutable

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

Instance (1):

/OwnableTwoSteps.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L13



## G-03 <x> += <y> costs more gas than <x> = <x> + <y> for state variables

Instances (3):

SeaportProxy.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L109
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L150
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L209


## G-04 internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Instances (8):

LowLevelERC1155Transfer.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L23-L29
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L45-L51

LowLevelERC721Transfer.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L21-L26

LowLevelERC20Transfer.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L24-L29
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L46-L50

LowLevelERC20Approve.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L20-L24

SignatureChecker.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L72-L76

TokenTransferrer.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenTransferrer.sol#L14-L20
 

## G-05 Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

Instances (4):

LooksRareAggregator.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/LooksRareAggregator.sol#L220

LooksRareProxy.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L132

SeaportProxy.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L86
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L217


## G-06 Use calldata instead of memory for function parameters

Having function arguments use calldata instead of memory can save gas. 

Recommended Mitigation Steps: 
Change function arguments from memory to calldata.

Intances (8):

LooksRareProxy.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L53
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L108
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/LooksRareProxy.sol#L109

TokenReceiver.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenReceiver.sol#L19
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenReceiver.sol#L27
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenReceiver.sol#L28
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/TokenReceiver.sol#L29

SignatureChecker.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/SignatureChecker.sol#L19



## G-07 Using calldata instead of memory for read-only arguments in external functions 

Using calldata instead of memory for read-only arguments in external functions saves gas.
When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. 
Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). 
Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one

Instances (2):

SeaportInterface.sol
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/SeaportInterface.sol#L166
https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/interfaces/SeaportInterface.sol#L240

