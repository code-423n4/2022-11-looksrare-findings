## Low

## Low-level transfers do not check contract existence

Low level token transfer helpers like `LowLevelERC20Transfer` make low level calls without checking that the target address is a contract. If there is no contract code at the target address, the call will succeed without actually executing a token transfer. If downstream contracts do not perform this check, this could allow malicious users to "transfer" nonexistent tokens in exchange for real ones. (In practice, this is not the case, see "Impact" below).

[`LowLevelERC20Transfer.sol`](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L24-L38)

```solidity
function _executeERC20TransferFrom(
  address currency,
  address from,
  address to,
  uint256 amount
) internal {
  (bool status, bytes memory data) = currency.call(
    abi.encodeWithSelector(IERC20.transferFrom.selector, from, to, amount)
  );

  if (!status) revert ERC20TransferFromFail();
  if (data.length > 0) {
    if (!abi.decode(data, (bool))) revert ERC20TransferFromFail();
  }
}

```

Other low level token transfer helpers similarly skip checking the code size of the call target:

- [`LowLevelERC20Approve#_executeERC20Approve`](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Approve.sol#L25)
- [`LowLevelERC20Transfer#_executeERC20DirectTransfer`](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC20Transfer.sol#L46)
- [`LowLevelERC721Transfer#_executeERC721TransferFrom`](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC721Transfer.sol#L21)
- [`LowLevelERC1155Transfer#_executeERC1155SafeTransferFrom`](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L23)
- [`LowLevelERC1155Transfer#_executeERC1155SafeBatchTransferFrom`](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/lowLevelCallers/LowLevelERC1155Transfer.sol#L45)

**Impact:**
Although it's possible to "transfer" a nonexistent token into the `LooksRareAggregator`, the existing `LooksRareProxy` and `SeaportProxy` will both revert on attempts to execute orders with nonexistent tokens. However, since the aggregator is designed to support additional generic proxies, it's important to ensure that any new proxies added to the system will correctly handle this case, and take extra caution wherever you're using these functions.

**Recommendation:**
Check the codesize of the call target before making a low-level call:

```solidity
function _executeERC20TransferFrom(
  address currency,
  address from,
  address to,
  uint256 amount
) internal {
  if (currency.code.length == 0) revert InvalidCurrency();
  (bool status, bytes memory data) = currency.call(
    abi.encodeWithSelector(IERC20.transferFrom.selector, from, to, amount)
  );

  if (!status) revert ERC20TransferFromFail();
  if (data.length > 0) {
    if (!abi.decode(data, (bool))) revert ERC20TransferFromFail();
  }
}

```

**Test cases:**

`LowLevelERC20Transfer.t.sol`:

```solidity
function testTransferFromERC20NoCode(uint256 amount)
  external
  asPrankedUser(_sender)
{
  lowLevelERC20Transfer.transferFromERC20(
    address(0),
    _sender,
    _recipient,
    amount
  );
}

function testTransferERC20NoCode(uint256 amount)
  external
  asPrankedUser(_sender)
{
  lowLevelERC20Transfer.transferERC20(address(0), _recipient, amount);
}

```

`LowLevelERC721Transfer.t.sol`:

```solidity
function testTransferFromERC721NoCode(uint256 tokenId)
  external
  asPrankedUser(_sender)
{
  lowLevelERC721Transfer.transferERC721(
    address(0),
    _sender,
    _recipient,
    tokenId
  );
}

```

`LowLevelERC1155Transfer.t.sol`:

```solidity
function testSafeTransferFromERC1155NoCode(uint256 tokenId, uint256 amount)
  external
  asPrankedUser(_sender)
{
  lowLevelERC1155Transfer.safeTransferFromERC1155(
    address(0),
    _sender,
    _recipient,
    tokenId,
    amount
  );
}

```

### `_tranferTokenToRecipient` overlaps ERC20 interface

Since ERC20 and ER721 both include a `transferFrom(address,address,uint256)` function, it's possible to pass an ERC20 token as the `collection` address to `TokenTransferrer#_transferTokenToRecipient` and transfer an amount of ERC20 tokens equal to the expected `tokenId`:

[`TokenTransferrer#_transferTokenToRecipient`](https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/TokenTransferrer.sol#L14-L26)

```solidity
function _transferTokenToRecipient(
  CollectionType collectionType,
  address recipient,
  address collection,
  uint256 tokenId,
  uint256 amount
) internal {
  if (collectionType == CollectionType.ERC721) {
    IERC721(collection).transferFrom(address(this), recipient, tokenId);
  } else if (collectionType == CollectionType.ERC1155) {
    IERC1155(collection).safeTransferFrom(
      address(this),
      recipient,
      tokenId,
      amount,
      ""
    );
  }
}

```

**Recommendation**
Consider using ERC721 `safeTransferFrom` instead, which does not overlap the ERC20 interface.
