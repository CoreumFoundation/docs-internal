# Run seed node

The doc describes the procedure of creating and running the seed node.

[Here](https://docs.tendermint.com/v0.34/tendermint-core/using-tendermint.html#seed) you can find additional information about the node type.

* [Check requirements](../src/validator/hardware-requirements.md)

* [Install cored binary](../src/validator/cored.md).

* Verify that [network variables](../src/validator/network-variables.md) are set up correctly.

* [Set up node prerequisites](../src/validator/node-prerequisites.md)

* Set the moniker variable to reuse it in the following instructions.
  ```bash
  export MONIKER="seed1"
  ```

* Init the node.

  ```bash
  cored init $MONIKER $COREUM_CHAIN_ID_ARGS
  ```
  The command will create a default node configuration

* Install the required util: `crudini`.

* Set the common connection config using the [doc](../src/validator/set-connection-config.md).

* Update the seed node config.

  ```bash
  COREUM_NODE_CONFIG=$COREUM_HOME/config/config.toml
  ```

  ```bash
  crudini --set $COREUM_NODE_CONFIG p2p seed_mode true
  ```

  In case you don't want to connect your seed to default seeds or in case it's the first launch - reset the seeds:
  ```bash
  crudini --set $COREUM_NODE_CONFIG p2p seeds "\"\"" 
  ```

* Capture the seed peer to be used for connection to it.
  ```bash
  echo "$(cored tendermint show-node-id)@$COREUM_EXTERNAL_IP:26656"
  ```

* Start the node.

  * Start with `cosmovisor` (recommended)
  ```bash
  cosmovisor run start $COREUM_CHAIN_ID_ARGS
  ```

  * Start with `cored`
   ```bash
  cored start $COREUM_CHAIN_ID_ARGS
  ```

  **Attention!** *Be sure that the node will be automatically started after starting a new terminal session. Add it as an OS "service",
  or schedule the start using the tools you prefer.*
