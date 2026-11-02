# MerkleRootRegistryZK - Zenroom test transactions

Zenroom (Zencode and lua) transactions that call the `MerkleRootRegistryZK` smart contract on SwissLedger testnet.

## Contract
1. Address: `0xcB423581fA4ca4201d8C71fF2399a3f11dD35F39`
2. Semaphore: `0xd37fc925fCF0878Adb8638aF6F9063384Dc584Ab` (group 1)
3. Testnet: SwissLedger chain 222
4. RPC: `https://testnet-swiss-ledger-rpc.noku.io`
5. Explorer: `https://explorertest.ledger.swiss/`

## Files
1. `swiss_merkleZK.lua`: Lua library with the "When" for the transactions
2. `*.zen`: Zencode files, one per "When"
3. `*.json`: input data for each transaction
4. `*_out.json`: signed transaction, already sent to the testnet
5. `*_153`, `*_155`, `*_157`, `*_159`: second run of `verify` and `validate`. `verify` passes again, `validate` is rejected (nullifier already used).


## How to run
Sign a transaction with Zenroom, using a keyring file with the private key (never commit it):
```
/path/to/zenroom -z -a file.json -k keyring.json -l swiss_merkleZK.lua file.zen > file_out.json
```
Then send it:
```
swissledger-cast publish --rpc-url https://testnet-swiss-ledger-rpc.noku.io 0x<signed transaction>
```
Always check the nonce first with `swissledger-cast nonce`.
