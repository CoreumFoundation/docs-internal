# Wrong block header recovery

Nodes might start producing a block with the same number but a different hash because of a mistake in the binary, as a
result, the chain is halted and nodes are stuck printing errors like this:

```bash
panic: precommit step; +2/3 prevoted for an invalid block: wrong Block.Header.AppHash Expected EA109DE893D3977CF0662EF235348F6F7F0BC20029306186EC51E91F175150D7, got DA20F96DE83B499E6F0EF0B639E7AA6CEDF3430405D192002D12C6578A4CAF90
```

The doc describes steps on how to recover the chain.

* Step 1 - fix the code issue

The `wrong Block.Header.AppHash` is produced because of non-deterministic operation execution in the code. Hence to let
the chain keep working the developers should find the issue and fix it.

* Step 2 - rebuild binary and update it on the node

Once the code is ready, rebuild the binary and update it on the nodes. If you use the `cored` directly, stop the node
and replace the binary. If you use `cosmovisor` stop it, and replace the binary from the latest
upgrade
folder.

* Step 3 - reset block

The block with invalid hash might be stored, so you need to reset it.

```bash
cored rollback --chain-id "{Chain ID}" # update the "{Chain ID}" with the proper value
```

Depending on your node setup there might be no historical state so the command will error out with

```bash
failed to rollback tendermint state: no blockstore found in ...
```

In that case, validate that the path to data folder in the error to the store is correct and proceed with the next step.

* Step 4 - start the node

Start node and check logs. 
