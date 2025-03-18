# Instructions to set up Coreum Foundation multi-signature wallets

## Important notes:

- keys in this guide are derived from the **same mnemonic** by incrementing account index in HD Path.
  It is implemented in this way because a single hardware wallet operates with a single mnemonic.
  Otherwise, it would require 5 separate hardware wallets.
- ATOM coin type (118) is used instead of CORE (990) in HD paths because Ledger limits HD paths to one/multiple registered by app and CORE is currently not registered in Cosmos Ledger app.
  If you need to import the keys into another wallet, the same derivation paths should be used.
- Final derivation paths (`m / purpose' / coin_type' / account' / change / address_index`) for generated keys are :
  -  m/44'/118'/0'/0/0
  -  m/44'/118'/1'/0/0
  -  m/44'/118'/2'/0/0
  -  m/44'/118'/3'/0/0
  -  m/44'/118'/4'/0/0

## Prerequisites

1. Install `cored` binary version `v1.0.0` or newer
2. Make sure installation is correct by running `cored version`
3. Connect your Ledger device and unlock it
4. Make sure you have Cosmos (ATOM) app v2.34.9 or above installed on you Ledger device
5. Open Cosmos app on you Ledger device

## Instructions

### Step 1: Generate 5 private keys

NOTE:
- you will be prompted 5 times on Ledger device to confirm key generation. After each key generation compare address shown on Ledger and in bash. They should match.
- these instructions generate private keys on Ledger device and store references to them locally. Actual keys are stored inside Ledger only.

#### Instruction for Bob
```bash
for i in {0..4}
do
  cored keys add coreum-foundation-$i-bob --chain-id=coreum-mainnet-1 --keyring-backend=os --ledger --coin-type=118 --account=$i
done
```

#### Instruction for Reza
```bash
for i in {0..4}
do
  cored keys add coreum-foundation-$i-reza --chain-id=coreum-mainnet-1 --keyring-backend=os --ledger --coin-type=118 --account=$i
done
```

### Step 2: Dump public keys into files

#### Instruction for Bob
```bash
for i in {0..4}
do
  cored keys show coreum-foundation-$i-bob --chain-id=coreum-mainnet-1 --pubkey > coreum-foundation-$i-bob-pubkey.json
done
```

#### Instruction for Reza
```bash
for i in {0..4}
do
  cored keys show coreum-foundation-$i-reza --chain-id=coreum-mainnet-1 --pubkey > coreum-foundation-$i-reza-pubkey.json
done
```

### Step 3: Exchange files containing public keys with each other

#### Instruction for Bob
Send the following 5 files containing public keys (`coreum-foundation-0-bob-pubkey.json`, `coreum-foundation-1-bob-pubkey.json` ...) to Reza.

#### Instruction for Reza
Send the following 5 files containing public keys (`coreum-foundation-0-reza-pubkey.json`, `coreum-foundation-1-reza-pubkey.json` ...) to Bob.

### Step 4: Import counterparty public keys from file to the local keystore

#### Instruction for Bob
```bash
for i in {0..4}
do
  cored keys add --chain-id=coreum-mainnet-1 --pubkey=`cat coreum-foundation-$i-reza-pubkey.json` coreum-foundation-$i-reza
done
```

#### Instruction for Reza
```bash
for i in {0..4}
do
  cored keys add --chain-id=coreum-mainnet-1 --pubkey=`cat coreum-foundation-$i-bob-pubkey.json` coreum-foundation-$i-bob
done
```

### Step 5: Create multi-signature wallets

#### Universal instruction for both Bob & Reza
```bash
for i in {0..4}
do
  cored keys add --chain-id=coreum-mainnet-1 coreum-foundation-$i --multisig="coreum-foundation-$i-bob,coreum-foundation-$i-reza" --multisig-threshold=2
done
```

### Step 6: Compare resulting foundation wallet addresses. They are supposed to match.
