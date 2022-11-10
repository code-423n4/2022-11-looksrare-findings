# 1. [G-1] Arithmetics: uncheck blocks for arithmetics operations that can't underflow/overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn't possible, some gas can be saved by using an `unchecked` block.

I suggest wrapping with an `unchecked` block here:

    File contracts/OwnableTwoSteps.sol, line 114:     earliestOwnershipRenouncementTime = block.timestamp + delay;

    File contracts/proxies/SeaportProxy.sol, line 109:     if (orders[i].currency == address(0)) ethValue += orders[i].price;
    File contracts/proxies/SeaportProxy.sol, line 147:     uint256 orderFee = (orders[i].price * feeBp) / 10000;
    File contracts/proxies/SeaportProxy.sol, line 150:     fee += orderFee;
    File contracts/proxies/SeaportProxy.sol, line 207:     uint256 orderFee = (price * feeBp) / 10000;
    File contracts/proxies/SeaportProxy.sol, line 209:     fee += orderFee;

# 2. [G-2] Storage: Emitting storage values

The values emitted shouldn't be read from storage. The existing memory values should be used instead in here:

    File contracts/OwnableTwoSteps.sol, line 114-116:     
           earliestOwnershipRenouncementTime = block.timestamp + delay;

           emit InitiateOwnershipRenouncement(earliestOwnershipRenouncementTime);

I suggest code replace:

           uint256 _earliestOwnershipRenouncementTime = block.timestamp + delay;
           earliestOwnershipRenouncementTime = _earliestOwnershipRenouncementTime;

           emit InitiateOwnershipRenouncement(_earliestOwnershipRenouncementTime);
