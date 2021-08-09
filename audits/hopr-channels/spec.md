# Scope

To be audited:

- [HoprChannels.sol](https://github.com/hoprnet/hoprnet/blob/06eb3b0898c49ab2eb3f69b9eb4f3e51f82bb637/packages/ethereum/contracts/HoprChannels.sol)

# Scope of Audit

In the first iteration of the HoprChannels audit, we want to ensure that the smart contract contains no vulnerabilities but unlike a normal audit, we don’t want to pay any attention to gas usage. It’s expected that the contract is audited as if it's going to run on the current Ethereum main chain.

## HoprChannels

[[source code](https://github.com/hoprnet/hoprnet/blob/06eb3b0898c49ab2eb3f69b9eb4f3e51f82bb637/packages/ethereum/contracts/HoprChannels.sol)]

HoprChannels should:

- be an ERC777 recipient
- be an ERC1820 implementer
- should allow user to send HOPR tokens to it only (HoprToken.sol)
  - must only be HOPR tokens
  - must require `userData` to be a specific length for it to call `fundChannelMulti`
  - decoded `userData` should allow for channel funding
- should allow user to announce herself
- should allow user fund & open a payment channel
  - source & destination must not be the same
  - source & destination must not be null addresses
  - fund amount must be larger than 0
  - channel must not be `PENDING_TO_CLOSE`
  - if channel is `CLOSED`, it should set it to `WAITING_FOR_COMMITMENT`
  - if channel is `WAITING_FOR_COMMITMENT` it should set it to `OPEN`
  - update channel state
    - channelEpoch
    - ticketEpoch
    - ticketIndex
  - emit channel update event
- should allow user to redeem a valid ticket
  - source & destination must not be the same
  - source & destination must not be null addresses
  - destination must be msg.sender
    the hash of the ticket parameters must be used to recover the source which is the signer of the signature
  - ticket must be a winning ticket
  - update spending channel state
    - ticketIndex
    - commitment
    - balance
  - update earning channel balance if its `OPEN`
    - else transfer tokens to redeemer
  - emit channel update event
- should allow user to initiate channel closure
  - source & destination must not be the same
  - source & destination must not be null addresses
  - channel must be `OPEN` or `WAITING_FOR_COMMITMENT`
  - update channel state
    - closure time to `now + secs until closure`
    - status to `PENDING_TO_CLOSE`
  - emit channel update event
- should allow user to finalize channel closure
  - source & destination must not be the same
  - source & destination must not be null addresses
  - channel status must be `PENDING_TO_CLOSE`
  - channel closureTime must have passed
  - update channel state
    - reset data
  - emit channel update event
- should allow user to bump a channel
  - source & destination must not be the same
  - source & destination must not be null addresses
  - increment `ticketEpoch`
  - if channel is `WAITING_FOR_COMMITMENT`, set it to `OPEN`
- bundled transactions (using MultiCall) should not open the smart contract to any loopholes

## Learning more about HoprChannels

Our [yellowpaper](https://github.com/hoprnet/hoprnet/blob/92b2d7d02fc34039648fdbadb78b4e91a44a7351/docs/yellowpaper/yellowpaper.pdf) described in detail how our protocol works.
Sections 5 & 6 are the most relevant for the audit, but we suggest to read it all to get a whole picture of the protocol.
