# Rotate relayer keys

## Setup env variables and `coreumbridge-xrpl-relayer`

Pass the [doc](../../src/xrpl-bridge/run-relayer.md).

## Set up multi-signing

Pass the [doc](multisign.md) to set up accounts and keys.

## Generate key rotation template file

```bash
coreumbridge-xrpl-relayer coreum tx rotate-keys /root/.coreumbridge-xrpl-relayer/new-keys.yaml --init-only --from-owner
```

## Fill the `new-keys` file

**!!! The`new-keys.yaml` file should contain ALL KEYs to replace !!!**

**Testnet** example

```yaml
relayers:
  - coreum_address: "testcore1u7ugwzc5chfxmwqmu6ng4qlgg0nhuf35z93qhn"
    xrpl_address: "radreSgDCnEZYhS3vD9sCyi69coVPqffUq"
    xrpl_pub_key: "034DC089908B82A0DCA15962265A679B46D95EDCC22381957AD85DD6A7C9097B62"
  - coreum_address: "testcore1qctu5fe87e4ckr2vw5p6p9z0u33a2qjzfr5p6l"
    xrpl_address: "rPvdRfrHWkcjnEgSv5nQdMuE8iykWkthcG"
    xrpl_pub_key: "0393E6D1DF3EA8B92A13E029DCCF89D8E5BB605B70A3B678E98FD105E8B6317920"
evidence_threshold: 1
```

**!!! Don't forget to update the evidence_threshold !!!**

## Manually check that all relayer addresses

Check that Coreum and XRPL accounts exist and funded.

## Create `rotate-keys` transaction.

```bash
coreumbridge-xrpl-relayer coreum tx rotate-keys /root/.coreumbridge-xrpl-relayer/new-keys.yaml --from-owner --generate-only
```

Save the json tx output to the `rotate-keys.json` file.

## Set up tx CLI utils

[set-up-tx-utils.md](set-up-tx-utils.md)

## Optimize tx fee

```bash
optimize-tx-fee rotate-keys.json
```

Validate the tx manually and share with the signer to sing and execute.

### (Optional setup, for the testnet only) - Signing and broadcasting

Use [doc](multisign.md) to signing and broadcast the tx.

## Create `resume-bridge` transaction.

Once they keys are rotated resume the bridge.

```bash
coreumbridge-xrpl-relayer coreum tx resume-bridge --from-owner --generate-only
```

Save the json tx output to the `resume-bridge.json` file.

## Optimize tx fee

```bash
optimize-tx-fee resume-bridge.json
```

### (Optional setup, for the testnet only) - Signing and broadcasting

Use [doc](multisign.md) to signing and broadcast the tx.
