# Scope

To be audited:

- [HoprDistributor.sol](https://github.com/hoprnet/hoprnet/blob/713a02a3ffcffd9ae817c24d09e5426cc153878a/packages/ethereum/contracts/HoprDistributor.sol)

# Scope of Audit

These smart contracts are expected to run on the current Ethereum main chain as described below.

## HoprDistributor

[[source code](https://github.com/hoprnet/hoprnet/blob/713a02a3ffcffd9ae817c24d09e5426cc153878a/packages/ethereum/contracts/HoprDistributor.sol)]

HoprDistributor is a smart contract that allows distribution of HOPR tokens for testnet participants (bounties), early token purchasers, advisors and team. It allows for creation schedules and allocations, so that then an eligible user who can claim their tokens. Schedules are named and comprise the release time relative to the start time (1 month, 2 months, etc) and how much (50%, 10%, etc). Allocations are assigned to a schedule and define who (eth address) and how much (1000 HOPR) the user is allowed to claim. A user may have only one allocation per schedule.

The HoprDistributor smart contract should:

- allow deployer to specify HoprToken address, start time and maximum mint amount on smart contract creation
- allow the owner to specify a different start time until the previously defined start time has been reached
- [schedule]
  - allow the owner to add a schedule
  - not accept the same schedule name more than once
  - not accept invalid schedules with different array lengths
  - not accept invalid schedules with durations that are not in ascending order
  - not allow schedules which in total distribute more than 100% of their allocation
- [allocation]
  - allow the owner should to add allocations
  - not allow for allocations to be added to schedules that do not exist
  - not accept invalid allocations with different array lengths
  - not allow the owner to add allocations that exceed the total amount of mintable tokens
  - allow any user to claim tokens if they are eligible for the specified schedule at that time
  - allow users to claim tokens that were lost due to rounding errors in their last duration in the schedule
  - allow any account to claim tokens for an eligible account so that the eligible account does not need to send the transaction or pay for gas themselves
- [revoke]
  - allow the owner to revoke a user from claiming their tokens
  - prevent an account to claim any tokens from the time that their allocation has been revoked, this includes tokens which were already due to be released but that users has not claimed yet - for sake of clarity such tokens should also not be claimable anymore
  - not allow the owner to revoke allocations or schedules that do not exist
