# Multi-signing

### Import multi-signing account to coreumbridge-xrpl-relayer KR to generate the txs

```bash
coreumbridge-xrpl-relayer coreum keys add owner-multisign --pubkey='{Owner 1 pub key},{Owner 2 pub key},{Owner 3 pub key}'
```

### Install cored and set up the cored network variables

[Install cored](../../src/tutorials/cored.md).

### (Optional setup, for the testnet only) Create multi-signing owner/admin coreumbridge-xrpl-relayer

```bash
cored keys add owner1 $COREUM_CHAIN_ID_ARGS

cored keys add owner2 $COREUM_CHAIN_ID_ARGS

cored keys add owner3 $COREUM_CHAIN_ID_ARGS

cored keys add owner-multisign --multisig "owner1,owner2,owner3" --multisig-threshold 2 $COREUM_CHAIN_ID_ARGS
```

!!! Save output mnemonics to a safe place to be able to recover the relayer later. !!!

Take the `owner-multisign` address and use it for the bootstrapping

## (Optional setup, for the testnet only) - Signing and broadcasting

##### Sign the tx

```bash
cored tx sign tx.json \
  --multisig $(cored keys show --address owner-multisign $COREUM_CHAIN_ID_ARGS) \
  --from owner1 --output-document tx-owner1-signature.json \
  $COREUM_CHAIN_ID_ARGS $COREUM_NODE_ARGS
  
cored tx sign tx.json \
  --multisig $(cored keys show --address owner-multisign $COREUM_CHAIN_ID_ARGS) \
  --from owner2 --output-document tx-owner2-signature.json \
  $COREUM_CHAIN_ID_ARGS $COREUM_NODE_ARGS
  
cored tx sign tx.json \
  --multisig $(cored keys show --address owner-multisign $COREUM_CHAIN_ID_ARGS) \
  --from owner3 --output-document tx-owner3-signature.json \
  $COREUM_CHAIN_ID_ARGS $COREUM_NODE_ARGS    
```

##### Combine the signatures

```bash
cored tx multisign tx.json owner-multisign \
  tx-owner1-signature.json \
  tx-owner2-signature.json \
  tx-owner3-signature.json \
  $COREUM_CHAIN_ID_ARGS $COREUM_NODE_ARGS > tx-signed.json
```

##### Broadcast transaction

```bash
cored tx broadcast tx-signed.json -y -b block $COREUM_CHAIN_ID_ARGS $COREUM_NODE_ARGS 
```


