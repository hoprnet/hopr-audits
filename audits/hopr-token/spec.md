# Scope

To be audited:

- [HoprToken.sol](https://github.com/hoprnet/hoprnet/blob/713a02a3ffcffd9ae817c24d09e5426cc153878a/packages/ethereum/contracts/HoprToken.sol)
- [ERC777Snapshot.sol](https://github.com/hoprnet/hoprnet/blob/713a02a3ffcffd9ae817c24d09e5426cc153878a/packages/ethereum/contracts/ERC777/ERC777Snapshot.sol)

# Scope of Audit

These smart contracts are expected to run on the current Ethereum main chain as described below.

## HoprToken

[[source code](https://github.com/hoprnet/hoprnet/blob/713a02a3ffcffd9ae817c24d09e5426cc153878a/packages/ethereum/contracts/HoprToken.sol)]

HoprToken is an ERC777 and ERC20 compatible token, it also allows for querrying balance data at a specific block. This should allow for snapshot-based governance such as snapshot.page (which currently works with an archive node but an on-chain snapshot read strategy might be implemented in the future). To achieve that we have [extended](https://github.com/hoprnet/hoprnet/blob/713a02a3ffcffd9ae817c24d09e5426cc153878a/packages/ethereum/contracts/ERC777/ERC777Snapshot.sol) OZ's ERC777 contract and implemented a snapshotting mechanism similar to the [minime token](https://github.com/Giveth/minime/blob/ea04d950eea153a04c51fa510b068b9dded390cb/contracts/MiniMeToken.sol).

The HoprToken smart contract should:

- be ERC20 compatible
- be ERC777 compatible
- allow a user with role "MINTER_ROLE" to be able to mint tokens
- not allow any other account to mint tokens
- allow multiple accounts to be minter at the same time
- have an admin account who can appoint appoint minters
- allow admin to transfer or revoke their ownership via OZ `AccessControl` contract
- expose snapshot functionality, specifically it should
  - expose a function `getBalanceOfAt` to return the balance of a user at specified block number which should
    - return the balance at the end of the block is taken if there were multiple transactions to/from the account during one block
    - return the balance at the end of the last block with a transfer to/from that account
    - return zero if no transfers happened yet to the account at the specified block
  - expose a function `getTotalSupplyAt` to return the totalSupply at specified block number, same mechanism as for balance
