# Register tokens

## Setup env variables and `coreumbridge-xrpl-relayer`

Pass the [doc](../../src/xrpl-bridge/run-relayer.md).

## Set up multi-signing

Pass the [doc](multisign.md) to set up accounts and keys.

## Generate token registration transactions (Testnet example)

### Generate Testnet utestcore Coreum token for registration

```bash
coreumbridge-xrpl-relayer coreum tx register-coreum-token \
    utestcore 6 3 500000000000000 1480000 \
    --from-owner --generate-only
```

Save the json tx output to the `register-utestcore-tx.json` file.

### Generate Testnet SOLO XRPL token for registration

```bash
coreumbridge-xrpl-relayer coreum tx register-xrpl-token \
    rHZwvHEs56GCmHupwjA4RY7oPA3EoAJWuN 534F4C4F00000000000000000000000000000000 3 400000000000000000000000 1500000000000000 \
    --from-owner --generate-only
```

Save the json tx output to the `register-solo-tx.json` file.

### Generate Testnet Custom XRPL token for registration (with negative sending precision)

```bash
coreumbridge-xrpl-relayer coreum tx register-xrpl-token \
     --from-owner --generate-only -- r3KEN5rWJL9c4gaFv4ri46R9mxCQwZmDRF LOL -2 100000000000000000000000 550000000000000
```

Save the json tx output to the `register-custom-xrpl-token-tx.json` file.

### Set up tx CLI utils

[set-up-tx-utils.md](set-up-tx-utils.md)

### Join txs

```bash
join-txs register-utestcore-tx.json register-solo-tx.json register-multipl-tokens.json
join-txs register-multipl-tokens.json register-custom-xrpl-token-tx.json register-multipl-tokens.json
```

Validate the tx manually and share with the signer to sing and execute.

##### (Optional setup, for the testnet only) - Signing and broadcasting

Use [doc](multisign.md) to signing and broadcast the tx.
