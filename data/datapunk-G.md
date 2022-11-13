Running 6 tests for test/foundry/LooksRareProxyBenchmark.t.sol:LooksRareProxyBenchmarkTest

original:
[PASS] testExecuteDirectlySingleOrder() (gas: 257225)
[PASS] testExecuteThroughAggregatorSingleOrder() (gas: 4562334)
[PASS] testExecuteThroughAggregatorTwoOrdersAtomic() (gas: 4761371)
[PASS] testExecuteThroughAggregatorTwoOrdersNonAtomic() (gas: 4761376)
[PASS] testExecuteThroughV0AggregatorSingleOrder() (gas: 3450078)
[PASS] testExecuteThroughV0AggregatorTwoOrders() (gas: 3644601)

G1:
`BasicOrder memory order = orders[i];` can be changed to `BasicOrder calldata order = orders[i];`
https://github.com/code-423n4/2022-11-looksrare/blob/e3b2c053f722b0ca2dce3a3eb06f64859b8b7a6f/contracts/proxies/LooksRareProxy.sol#L65

[PASS] testExecuteDirectlySingleOrder() (gas: 257225)
[PASS] testExecuteThroughAggregatorSingleOrder() (gas: 4521877)
[PASS] testExecuteThroughAggregatorTwoOrdersAtomic() (gas: 4719113)
[PASS] testExecuteThroughAggregatorTwoOrdersNonAtomic() (gas: 4719118)
[PASS] testExecuteThroughV0AggregatorSingleOrder() (gas: 3409597)
[PASS] testExecuteThroughV0AggregatorTwoOrders() (gas: 3602319)



G2. change `function _splitSignature(bytes memory signature)` to `function _splitSignature(bytes calldata signature)`
as well as:
        if (signature.length == 64) {
            bytes32 vs;
            assembly {
                // r := mload(add(signature, 0x20))
                // vs := mload(add(signature, 0x40))
                r := calldataload(add(signature.offset, 0x00))
                vs := calldataload(add(signature.offset, 0x20))
                s := and(vs, 0x7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff)
                v := add(shr(255, vs), 27)
            }
        } else if (signature.length == 65) {
            assembly {
                // r := mload(add(signature, 0x20))
                // s := mload(add(signature, 0x40))
                // v := byte(0, mload(add(signature, 0x60)))
                r := calldataload(add(signature.offset, 0x00))
                s := calldataload(add(signature.offset, 0x20))
                v := byte(0, calldataload(add(signature.offset, 0x40)))
            }

[PASS] testExecuteDirectlySingleOrder() (gas: 257225)
[PASS] testExecuteThroughAggregatorSingleOrder() (gas: 4510093)
[PASS] testExecuteThroughAggregatorTwoOrdersAtomic() (gas: 4707165)
[PASS] testExecuteThroughAggregatorTwoOrdersNonAtomic() (gas: 4707170)
[PASS] testExecuteThroughV0AggregatorSingleOrder() (gas: 3397805)
[PASS] testExecuteThroughV0AggregatorTwoOrders() (gas: 3590363)
