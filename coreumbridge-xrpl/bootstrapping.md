# Bootstrap the bridge

## Init first relayer

Use [doc](../../src/xrpl-bridge/run-relayer.md) to init the relayer (don't run for now).

## Import pre-generate bridge-account key

```bash
coreumbridge-xrpl-relayer xrpl keys add bridge-account --recover
```

Use the pre-generate mnemonic for the fancy name

## Generate new key which will be used for the contract deployment

```bash
coreumbridge-xrpl-relayer coreum keys add contract-deployer
```

Send some core tokens to the generated address, to have enough for the contract deployment.

## Generate config template

```bash
coreumbridge-xrpl-relayer bootstrap-bridge /root/.coreumbridge-xrpl-relayer/bootstrapping.yaml \
  --xrpl-key-name bridge-account --coreum-key-name contract-deployer --init-only --relayers-count 32
```

The output will print the XRPL bridge address and min XRPL bridge account balance. Fund it and
proceed to the nex step.

Output example:

```bash
Access to keyring requested
    keyring: "xrpl"
    key: "bridge-account"
XRPL bridge address
    address: "rB68zzifRoNEBLTepPxuPo3Sse5ok7z7Lw"
Access to keyring requested
    keyring: "coreum"
    key: "contract-deployer"
Coreum deployer address
    address: "testcore1v8p2lvlrc88jpr7r9ca4l3gc48hhu9ghfju2tg"
Initializing default bootstrapping config
    path: "/root/.coreumbridge-xrpl-relayer/bootstrapping.yaml"
Computed minimum XRPL bridge balance
    balance: 532
```

## Modify the `bootstrapping.yaml` config

Collect the config from the relayer and modify the bootstrapping config.

Config example:

```yaml
owner: ''
admin: ''
relayers:
    - coreum_address: ''
      xrpl_address: ''
      xrpl_pub_key: ''
evidence_threshold: 2
used_ticket_sequence_threshold: 150
trust_set_limit_amount: '340000000000000000000000000000000000000'
contract_bytecode_path: ''
xrpl_base_fee: 10
```

If you don't have the contract bytecode download it.

## Run the bootstrapping

```bash
coreumbridge-xrpl-relayer bootstrap-bridge /root/.coreumbridge-xrpl-relayer/bootstrapping.yaml \
  --xrpl-key-name bridge-account --coreum-key-name contract-deployer
```

## Setup env variables and `coreumbridge-xrpl-relayer`

Once the command is executed get the bridge contract address and update [doc](../../src/xrpl-bridge/install-relayer.md).
Update the initial relayer settings as well.

## Remove the bridge-account key

```bash
coreumbridge-xrpl-relayer xrpl keys delete bridge-account
```

## Run first relayer (skip the `init` part)

Use [doc](../../src/xrpl-bridge/run-relayer.md) to run the initial relayer.

## Prepare initial transactions

### Set up multi-signing

Pass the [doc](multisign.md) to set up accounts and keys.

### Generate tickets recovery transaction

```bash
coreumbridge-xrpl-relayer coreum tx recover-tickets --tickets-to-allocate 250 --from-owner --generate-only
```

Save the json tx output to the `allocate-tickets-tx.json` file.

### Generate XRP bridging fee update transaction

```bash
# 0.87 XRP
coreumbridge-xrpl-relayer coreum tx update-xrpl-token rrrrrrrrrrrrrrrrrrrrrhoLvTp XRP --bridging-fee 870000 --sending-precision 1 --from-owner --generate-only
```

Save the json tx output to the `set-xrp-fee-tx.json` file.

### Join txs into one

#### Set up tx CLI utils

[set-up-tx-utils.md](set-up-tx-utils.md)

#### Join txs

```bash
join-txs allocate-tickets-tx.json set-xrp-fee-tx.json allocate-tickets-and-set-xrp-fee-tx.json
```

Validate the tx manually and share with the signer to sing and execute.

##### (Optional setup, for the testnet only) - Signing and broadcasting

Use [doc](multisign.md) to signing and broadcast the tx.
