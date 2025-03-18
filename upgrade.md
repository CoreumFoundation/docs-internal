# Upgrade guide

1. When upgrade happen, Cosmovisor makes snapshot, ensure that validator has enough disk space, and notify other validators.
2. Update [public upgrade doc](../src/validator/upgrade.md) with new upgrade.
   - you should calculate the upgrade height, and leave enough time (at least one week) between the proposal end and the start of the upgrade.
   Also, it is convenient when upgrade happens in working hours, even at the daily, choose time wisely.
3. Notify validators about the future proposal and upgrade dates, and share the link to the upgrade doc. 
4. Create a proposal.
#### v3.0.0+
1. Draft proposal in JSON
   ```bash
   export CHAIN_ID=coreum-testnet-1
   export RPC_URL=https://full-node.testnet-1.coreum.dev:26657
   export YOUR_ADDRESS={YOUR_ADDRESS}

   # Draft proposal and fill needed info. For msg authority use default value (gov module account).
   # For now we don't upload proposal metadata to IPFS but we might do it later
   cored tx gov draft-proposal --chain-id=$CHAIN_ID --node=$RPC_URL

      Enter proposal title: Coreum v3patch1 Software Upgrade
      Enter proposal authors: testcore1efkcsd94u0vrx8rgq9cktjgq7fgwrjap3qu289
      Enter proposal summary: Coreum v3.0.2 version has been released containing bugfixes for v3.0.0. This upgrade will only be executed on testnet.
      Enter proposal details: Coreum v3.0.2 version has been released containing bugfixes for v3.0.0. This upgrade will only be executed on testnet.
      Enter proposal proposal forum url: https://github.com/CoreumFoundation/coreum/releases/tag/v3.0.2
      Enter proposal vote option context: yes
      Enter proposal deposit: 4000000000utestcore
      Enter msg authority: testcore10d07y265gmmuvt4z0w9aw880jnsr700jlcsr77
      The draft proposal has successfully been generated.
      Proposals should contain off-chain metadata, please upload the metadata JSON to IPFS.
      Then, replace the generated metadata field with the IPFS CID.
   ```

2. Manually edit draft_proposal.json and put correct information: name, height, info & metadata.

   Example:
   ```json
   {
      "messages": [
         {
            "@type": "/cosmos.upgrade.v1beta1.MsgSoftwareUpgrade",
            "authority": "testcore10d07y265gmmuvt4z0w9aw880jnsr700jlcsr77",
            "plan": {
               "name": "v3patch2",
               "time": "0001-01-01T00:00:00Z",
               "height": "15684437",
               "info": "https://github.com/CoreumFoundation/coreum/releases/tag/v3.0.2",
               "upgraded_client_state": null
            }
         }
      ],
      "metadata": "https://docs.coreum.dev/validator/upgrade.html",
      "deposit": "4000000000utestcore",
      "title": "Coreum v3patch2 Software Upgrade",
      "summary": "Coreum v3.0.2 version has been released containing bugfixes for v3.0.0. This upgrade will only be executed on testnet."
   }
   ```

3. Submit the proposal

```bash
cored tx gov submit-proposal draft_proposal.json --node=$RPC_URL --from=$YOUR_ADDRESS --chain-id=coreum-testnet-1 --gas=500000
```

#### Prior to v3.0.0
   - Don't forget to add `--upgrade-info`, since it goes to the following upgrade plan.
   - For a voting start you should provide a deposit(check amount in params), ensure you have this in your balance.
   - Edit variables corresponding to your upgrade:
   ```bash
   export DENOM=utestcore
   export CHAIN_ID=coreum-testnet-1
   export RPC_URL=https://full-node.testnet-1.coreum.dev:26657
   export YOUR_ADDRESS={YOUR_ADDRESS}

   export UPGRADE_NAME=v3
   export UPGRADE_INFO="Coreum v3 Software Upgrade"
   export UPGRADE_HEIGHT=14980000
   export TITLE="Coreum v3 Software Upgrade"
   export DEPOSIT=4000000000
   export DESCRIPTION="The implementation of Coreum V3 has been successfully completed. With upgrades ranging from Cosmos SDK to Custom Modules, this milestone significantly enhances the Coreum blockchain..."
   ```
   - Send submit-proposal tx: 
   ```bash
   # Note that we need more gas because of likely long description.
   cored tx gov submit-proposal software-upgrade \
    "$UPGRADE_NAME" \
    --upgrade-info="$UPGRADE_INFO" \
    --upgrade-height=$UPGRADE_HEIGHT \
    --title="$TITLE" \
    --description="$DESCRIPTION" \
    --from $YOUR_ADDRESS \
     --deposit=$DEPOSIT$DENOM \
    --chain-id=$CHAIN_ID \
    --node=$RPC_URL \
    --gas=500000
   ```
5. Notify validators about the proposal.
6. Vote for it. You should have access to the keys to conclude a vote.
   ```bash
   export YOUR_ADDRESS={YOUR_ADDRESS}
   export PROPOSAL_ID={PROPOSAL_ID}
   export RPC_URL=https://full-node.testnet-1.coreum.dev:26657
   export CHAIN_ID=coreum-testnet-1
   
   cored tx gov vote $PROPOSAL_ID yes --from $YOUR_ADDRESS --chain-id=$CHAIN_ID --node=$RPC_URL
   ```
   
If vote passed, do the following. If not, stop at this point.

7. Pass the Upgrade name to our DevOps team to start placing binary to our validators.
8. Prepare docs update with new binary version and update [upgrade history](../src/validator/upgrade-history.md)
9. After the Upgrade event, you may verify that the right binary is running by executing this command:
```bash
ps aux | grep cored
```
