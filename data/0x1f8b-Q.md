- [Low](#low)
    - [**1. Mixing and Outdated compiler**](#1-mixing-and-outdated-compiler)
    - [**2. One token will be locked forever**](#2-one-token-will-be-locked-forever)
    - [**3. Lack of checks supportsInterface**](#3-lack-of-checks-supportsinterface)
- [Non critical](#non-critical)
    - [**4. Use abstract for base contracts**](#4-use-abstract-for-base-contracts)
    - [**5. Avoid hardcoded values**](#5-avoid-hardcoded-values)
    - [**6. IsAtomic design**](#6-isatomic-design)
    - [**7. Use the same argument order**](#7-use-the-same-argument-order)

# Low

## **1. Mixing and Outdated compiler**

The pragma version used are:

```
pragma solidity ^0.8.0;
pragma solidity ^0.8.14;
pragma solidity 0.8.17;
pragma solidity >=0.5.0;
```

*Note that mixing pragma is not recommended. Because different compiler versions have different meanings and behaviors, it also significantly raises maintenance costs. As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues.*

The minimum required version must be [0.8.17](https://github.com/ethereum/solidity/releases/tag/v0.8.17); otherwise, contracts will be affected by the following **important bug fixes**:

## **2. One token will be locked forever**

When recovering lost tokens, one is always left in the contract, which produces an underflow if there is no balance, and in the worst case, leaves a token always locked.

*I decided to mark this issue as low because the amount of tokens, although this subtraction is not reported in any comment of the code, nor is it justified. But keep in mind that depending on which tokens, a fraction could be of high value.*

```diff
contract TokenRescuer is OwnableTwoSteps, LowLevelETH, LowLevelERC20Transfer {
    error InsufficientAmount();

    /**
     * @notice Rescue the contract's trapped ETH
     * @dev Must be called by the current owner
     * @param to Send the contract's ETH balance to this address
     */
    function rescueETH(address to) external onlyOwner {
-       uint256 withdrawAmount = address(this).balance - 1;
        if (withdrawAmount == 0) revert InsufficientAmount();
        _transferETH(to, withdrawAmount);
    }

    /**
     * @notice Rescue any of the contract's trapped ERC20 tokens
     * @dev Must be called by the current owner
     * @param currency The address of the ERC20 token to rescue from the contract
     * @param to Send the contract's specified ERC20 token balance to this address
     */
    function rescueERC20(address currency, address to) external onlyOwner {
-       uint256 withdrawAmount = IERC20(currency).balanceOf(address(this)) - 1;
        if (withdrawAmount == 0) revert InsufficientAmount();
        _executeERC20DirectTransfer(currency, to, withdrawAmount);
    }
}
```

**Affected source code:**

- [TokenRescuer.sol:23](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/TokenRescuer.sol#L23)
- [TokenRescuer.sol:35](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/TokenRescuer.sol#L35)

## **3. Lack of checks `supportsInterface`**

The `EIP-165` standard helps detect that a smart contract implements the expected logic, prevents human error when configuring smart contract bindings, so it is recommended to check that the received argument is a contract and supports the expected interface.

**Reference:**

- https://eips.ethereum.org/EIPS/eip-165

**Affected source code:**

- [ERC20EnabledLooksRareAggregator.sol:22](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/ERC20EnabledLooksRareAggregator.sol#L22)
- [LooksRareProxy.sol:38-39](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/LooksRareProxy.sol#L38-L39)
- [SeaportProxy.sol:46-47](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/SeaportProxy.sol#L46-L47)

---

# Non critical

## **4. Use `abstract` for base contracts**

`abstract` contracts are contracts that have at least one function without its implementation. **An instance of an abstract cannot be created.**

**Reference:**

- https://docs.soliditylang.org/en/v0.6.2/contracts.html#abstract-contracts


**Affected source code:**

The following contracts would be more convenient to be `abstract`:

- [TokenRescuer.sol:14](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/TokenRescuer.sol#L14)

The following contracts would be more convenient to implement as libraries:

- [LowLevelERC1155Transfer.sol:11](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L11)
- [LowLevelERC20Approve.sol:11](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L11)
- [LowLevelERC20Transfer.sol:13](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L13)
- [LowLevelERC721Transfer.sol:11](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L11)
- [LowLevelETH.sol:11](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/lowLevelCallers/LowLevelETH.sol#L11)
- [TokenTransferrer.sol:13](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/TokenTransferrer.sol#L13)

## **5. Avoid hardcoded values**

It is not good practice to hardcode values, but if you are dealing with addresses much less, these can change between implementations, networks or projects, so it is convenient to remove these values from the source code.

**Affected source code:**

Use the selector instead of the raw value:

- `0x1626ba7e` > [SignatureChecker.sol:80](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/SignatureChecker.sol#L80)

Use a constant for `10000` in:

- [SeaportProxy.sol:147](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/SeaportProxy.sol#L147)
- [SeaportProxy.sol:207](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/SeaportProxy.sol#L207)
- [LooksRareAggregator.sol:158](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/LooksRareAggregator.sol#L158)

## **6. IsAtomic design**

I don't see any advantage of having a global `isAtomic` argument to all commands, when this task can be done by the caller, it would make more sense to be able to independently execute one of all commands given in `isAtomic` way, so that argument should remain to the `MakerOrder` structure.

```diff
    function execute(
        BasicOrder[] calldata orders,
        bytes[] calldata ordersExtraData,
        bytes memory,
        address recipient,
-       bool isAtomic,
        uint256,
        address
    ) external payable override {
```

**Affected source code:**

- [LooksRareProxy.sol:55](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/LooksRareProxy.sol#L55)
- [SeaportProxy.sol:65](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/proxies/SeaportProxy.sol#L65)
- [LooksRareAggregator.sol:56](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/LooksRareAggregator.sol#L56)

## **7. Use the same argument order**

In `LooksRareAggregator.approve`, the argument order is different than in `_executeERC20Approve`, so it is prone to human error.

```diff
    function approve(
-       address marketplace,
        address currency,
+       address marketplace,
        uint256 amount
    ) external onlyOwner {
        _executeERC20Approve(currency, marketplace, amount);
    }
```

**Affected source code:**

- [LooksRareAggregator.sol:171-177](https://github.com/code-423n4/2022-11-looksrare/blob/d8949e1c527e0544027370969f970c4194b10640/contracts/LooksRareAggregator.sol#L171-L177)
