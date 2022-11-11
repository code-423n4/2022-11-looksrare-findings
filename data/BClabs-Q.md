
Non critical:
In OwnableTwoSteps it says delay must be set by inheriting contract:
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/OwnableTwoSteps.sol#L40
But none of the contracts actually set it.

QA:
The owner should be careful when setting functions since a marketplace contract can be upgradeable, which could cause functions to change and DOS until removeFunction is called. So maybe just a heads-up for the owner to disallow upgradable proxies.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/LooksRareAggregator.sol#L132-L135

QA:
When trying to refer to the native token using an address its better to use the 0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE address instead of address(0).

QA: Require input addresses to not be equal 0.
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L37-L40

QA: If owner decides to renounce ownership using confirmOwnershipRenouncement, then ERC721 and ERC1155 
tokens that are trapped in the contract can never be rescued.

QA: Abstract contracts are contracts that don't have some functions implemented. In some cases such as SignatureChecker it would be better to declare the contract a library.

QA: Interfaces should be declared as such not as contracts
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/interfaces/IOwnableTwoSteps.sol#L7

