[1] ``<ARRAY>.LENGTH`` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A`` FOR``-LOOP

The overheads outlined below are *PER LOOP*, excluding the first loop

-storage arrays incur a Gwarmaccess (100 gas)
-memory arrays use MLOAD (3 gas)
-calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset.

*There are 2 instances of this issue:*

```
File: 2022-11-looksrare/contracts/proxies/SeaportProxy.sol

126:  for (uint256 i; i < availableOrders.length; ) {

187:   for (uint256 i; i < orders.length; ) {

```
[https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L126](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L126)

[https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L187](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L187)


[2]  USAGE OF ``UINTS/INTS`` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

*There are 3 instances of this issue*

```
File:  2022-11-looksrare/contracts/proxies/SeaportProxy.sol

31:  uint120 numerator;

32:  uint120 denominator;

246:   offer[0].itemType = ItemType(uint8(order.collectionType) + 2);
```

[https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L31-L32](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L31-L32)


[https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L246](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/proxies/SeaportProxy.sol#L246)



[3] FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED ``PAYABLE``

If a function modifier such as ``onlyOwner`` is used, the function will revert if a normal user tries to pay the function. Marking the function as ``payable`` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are ``CALLVALUE``(2),``DUP1``(3),``ISZERO``(3),``PUSH``2(3),``JUMPI``(10),``PUSH1``(3),``DUP1``(3),``REVERT``(0),``JUMPDEST``(1),``POP``(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

*There are 4 instances of this issue:*

```
File:  2022-11-looksrare/contracts/OwnableTwoSteps.sol

51: function cancelOwnershipTransfer() external onlyOwner {

68:   function confirmOwnershipRenouncement() external onlyOwner {

98:   function initiateOwnershipTransfer(address newPotentialOwner) external onlyOwner {

110:   function initiateOwnershipRenouncement() external onlyOwner {
```

[https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L51](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L51)

[https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L68](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L68)

[https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L98](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L98)

[https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L110](https://github.com/code-423n4/2022-11-looksrare/blob/main/contracts/OwnableTwoSteps.sol#L110)



