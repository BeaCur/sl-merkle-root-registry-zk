# MerkleRootRegistryZK - Zenroom test transactions

Zenroom (Zencode and lua) transactions that call the `MerkleRootRegistryZK` smart contract on SwissLedger testnet.

## Contract
1. Address: `0xcB423581fA4ca4201d8C71fF2399a3f11dD35F39`
2. Semaphore: `0xd37fc925fCF0878Adb8638aF6F9063384Dc584Ab` (group 1)
3. Testnet: SwissLedger chain 222
4. RPC: `https://testnet-swiss-ledger-rpc.noku.io`
5. Explorer: `https://explorertest.ledger.swiss/`

These are the addresses used for the manual tests. The CI deploys new contracts at every run.

## Files
1. `swiss_merkleZK.lua`: Lua library with the "When" for the transactions
2. `*.zen`: Zencode files, one per "When"
3. `*.json`: input data for each transaction (not used by the CI)
4. `*_out.json`: signed transaction, already sent to the testnet
5. `*_153`, `*_155`, `*_157`, `*_159`: second run of `verify` and `validate`. `verify` passes again, `validate` is rejected (nullifier already used)
6. `*_160`: `transferOwnership` to a new wallet (to test `acceptOwnership`)
7. Copied from https://github.com/LuganoPlanB/swissledger-anonset, used by the CI:
   - `foundry.toml`: configuration of Foundry
   - `package.json`: Node.js packages installed by the workflow
   - `src/`: the contract and its `BuildInfo`
   - `script/`: deploy script (Semaphore verifier, Semaphore, contract)
   - `vendor/`: Poseidon hash library (used by Semaphore)
   - `clients/anonset/`: creates identities and generates proofs
8. `test/run.sh`: script run by the workflow. It signs and sends the transactions and checks the result
9. `.github/workflows/test.yml`: workflow for GitHub Actions

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

## CI
`test.yml` has two jobs: both deploy the contracts (Semaphore verifier, Semaphore and `MerkleRootRegistryZK`) and run `test/run.sh`. It tests all the functions in the `.zen` files.
1. `unit`: runs on every push, on a local anvil chain
2. `smoke`: runs only when started by hand, using "Run workflow", after `unit` passed. It uses the SwissLedger testnet and needs two secrets: `WALLET1_KEY` (deploys the contracts and signs the transactions) and `WALLET2_KEY` (second wallet, used as a new member manager and owner).
