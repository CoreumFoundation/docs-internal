# Instructions to send a sample TX using Coreum Foundation multi-signature wallet

## Prerequisites

1. Install `cored` binary version `v1.0.0` or newer
2. Make sure installation is correct by running `cored version`
3. Install [`jq`](https://stedolan.github.io/jq/download/) binary (only for Reza)

## Instructions

**Between generating a signature and broadcasting final multi-sig transaction, no other transaction should be
signed/broadcast from the same accounts (both multi-sig and personal). Otherwise, the original one fails.
Preferred way is to include multiple messages inside a single tx instead of creating multiple txs.
Please note that this requires manual editing of JSON file containing tx and might require increase of gas.**

### The following variables are used inside the scripts:
```bash
export FOUNDATION_MULTISIG_WALLET_NAME="" # Name of the foundation multi-sig wallet. E.g coreum-foundation-0.
export UNSIGNED_TX_FILE_NAME=""           # Name of the temporary file in CURRENT FOLDER containing unsigned tx in JSON format. E.g tx-staking-delegate-unsigned.json.
```

### Step 1 (Reza): Generate unsigned transaction and save to file

#### Staking Delegate example

```bash
export FOUNDATION_MULTISIG_WALLET_NAME=""
export UNSIGNED_TX_FILE_NAME=""

export VALIDATOR_ADDRESS="" # Address of the validator to delegate funds to. E.g corevaloper1d5wqdp322zn5jyn5mszrrstg2xuq35xyefwa9h
export DELEGATION_AMOUNT="" # Amount of ucore to be delegated. E.g 1000000ucore (equal to 1core)

# Set timeout height for tx in approximately 24h (50k blocks). This option is added to prevent signing & publishing the same tx twice.
# To disable this you can just set TIMEOUT_HEIGHT to 0.
export TIMEOUT_HEIGHT=$(($(cored q block --node="https://full-node.mainnet-1.coreum.dev:26657" | jq '.block.header.height' -r) + 50000))

cored tx staking delegate \
  $VALIDATOR_ADDRESS \
  $DELEGATION_AMOUNT \
  --chain-id=coreum-mainnet-1 \
  --from=$(cored keys show --address "$FOUNDATION_MULTISIG_WALLET_NAME" --chain-id=coreum-mainnet-1) \
  --gas-prices="0.0625ucore" \
  --gas=500000 \
  --timeout-height=$TIMEOUT_HEIGHT \
  --generate-only > "$UNSIGNED_TX_FILE_NAME"
```

#### Bank Send example

```bash
export FOUNDATION_MULTISIG_WALLET_NAME=""
export UNSIGNED_TX_FILE_NAME=""

export RECIPIENT_ADDRESS="" # Address of the transfer recipient. E.g core1d5wqdp322zn5jyn5mszrrstg2xuq35xyrhsc9f
export SEND_AMOUNT=""       # Amount of funds to be sent. E.g 1000000ucore (equal to 1core)

# Set timeout height for tx in approximately 24h (50k blocks). This option is added to prevent signing & publishing the same tx twice.
# To disable this you can just set TIMEOUT_HEIGHT to 0.
export TIMEOUT_HEIGHT=$(($(cored q block --node="https://full-node.mainnet-1.coreum.dev:26657" | jq '.block.header.height' -r) + 50000))

cored tx bank send \
  $(cored keys show --address "$FOUNDATION_MULTISIG_WALLET_NAME" --chain-id=coreum-mainnet-1) \
  $RECIPIENT_ADDRESS \
  $SEND_AMOUNT \
  --chain-id=coreum-mainnet-1 \
  --gas-prices="0.0625ucore" \
  --gas=500000 \
  --timeout-height=$TIMEOUT_HEIGHT \
  --generate-only > "$UNSIGNED_TX_FILE_NAME"
```

### Step 2 (Reza): Send file with unsigned tx to Bob & specify the key which is supposed to sign it

### Step 3 (Reza & Bob): Sign tx

Before doing this step makesure to:
1. Connect your Ledger device and unlock it
2. Make sure you have Cosmos (ATOM) app v2.34.9 or above installed on you Ledger device
3. Open Cosmos app on you device

#### Instruction for Bob
```bash
export FOUNDATION_MULTISIG_WALLET_NAME=""
export UNSIGNED_TX_FILE_NAME=""

cored tx sign "$UNSIGNED_TX_FILE_NAME" \
  --chain-id=coreum-mainnet-1 \
  --node="https://full-node.mainnet-1.coreum.dev:26657" \
  --multisig="$FOUNDATION_MULTISIG_WALLET_NAME" \
  --from="$FOUNDATION_MULTISIG_WALLET_NAME"-bob \
  --keyring-backend=os \
  --ledger \
  --output-document bob-signature-for-"$UNSIGNED_TX_FILE_NAME"
```

#### Instruction for Reza
```bash
export FOUNDATION_MULTISIG_WALLET_NAME=""
export UNSIGNED_TX_FILE_NAME=""

cored tx sign "$UNSIGNED_TX_FILE_NAME" \
  --chain-id=coreum-mainnet-1 \
  --node="https://full-node.mainnet-1.coreum.dev:26657" \
  --multisig="$FOUNDATION_MULTISIG_WALLET_NAME" \
  --from="$FOUNDATION_MULTISIG_WALLET_NAME"-reza \
  --keyring-backend=os \
  --ledger \
  --output-document reza-signature-for-"$UNSIGNED_TX_FILE_NAME"
```

### Step 4 (Bob): Send file containing tx signature to Reza. 

File name example `bob-signature-for-tx-staking-delegate-unsigned.json`

### Step 5 (Reza): Add signatures to unsigned tx.

```bash
export FOUNDATION_MULTISIG_WALLET_NAME=""
export UNSIGNED_TX_FILE_NAME=""

cored tx multisign "$UNSIGNED_TX_FILE_NAME" \
  "$FOUNDATION_MULTISIG_WALLET_NAME" \
  bob-signature-for-"$UNSIGNED_TX_FILE_NAME" \
  reza-signature-for-"$UNSIGNED_TX_FILE_NAME" \
  --node="https://full-node.mainnet-1.coreum.dev:26657" \
  --chain-id=coreum-mainnet-1 > signed-"$UNSIGNED_TX_FILE_NAME"
```

### Step 6 (Reza): Broadcast tx.

```bash
export UNSIGNED_TX_FILE_NAME=""

cored tx broadcast signed-"$UNSIGNED_TX_FILE_NAME" -b block --node="https://full-node.mainnet-1.coreum.dev:26657"
```
