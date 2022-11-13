1.

Grammer issues in comments in:

1.1

Contract: ILooksRareAggregator.sol

1.1.1

	line 21

	"hardcoded" should be two words "hard coded"	

Modified Comment:

	* @param originator The address that originated the transaction, hard coded as msg.sender if it is called directly

1.2

Contract: SeaportInterface.sol

1.2.1

	lines 83, and 191

	"offer" should be "offers"

Modified Comments:

	*                            Also note that all offers and consideration

	*                                  note that all offers and consideration

1.2.2

	line 94

	"indicates" should be "indicate"

Modified Comment:

	*                            empty criteria indicate that any

1.2.3

	lines 96, and 295

	the "that" before "no" is unnecessary

Modified Comments:

	*                            in question is valid and no associated

	*                          token identifier is valid and no associated

1.2.4
	
	line 362

	the "the" before "fraction" should be "a"
	
Modified Comment:	
	
	*         the order has been cancelled or validated and a fraction of the

1.3

Contract: ConsiderationStructs.sol

1.3.1

	line 41

	"support" should be "supports"

Modified Comment:

	*      depending on the item type, and a start and end amount that supports

1.3.2

	line 168

	"are" should be "is"

Modified Comment:

	*      type is restricted and the offerer or zone is not the caller.

1.3.3

	line 209

	"offer" should be "offers"

Modified Comment:

	*      element. A given fulfillment can be applied to as many offers and

1.4

Contract: LooksRareProxy.sol

1.4.1

	line 18

	"batch buy" should be hyphenated as "batch-buy"

Modified Comment:

	* @notice This contract allows NFT sweepers to batch-buy NFTs from LooksRare

1.5

Contract: LooksRareAggregator.sol

1.5.1

	line 39

	"to" before the last "this" should be "for"

Modified Comment:

	*         this contract's ownership is compromised. By not providing any allowances for this

1.6

Contract: SignatureChecker.sol

1.6.1

	line 9
	
	"length" should be "lengths"

Modified Comment:

	* @notice This contract is used to verify signatures for EOAs (with lengths of both 65 and 64 bytes) and contracts (ERC-1271).

1.6.2

	line 59

	"signer" should be "signer's"

Modified Comment:

	// If the signature is valid (and not malleable), return the signer's address

2.

Two unnecessary lines open at the beginning of the contract LowLevelERC20Transfer.sol between the start of the contract and the License-Identifier.

Consider removing the two unnecessary open lines and starting the License-Identifier in line 1 as a best practice.

This will also bring consistency to all contracts under scope.