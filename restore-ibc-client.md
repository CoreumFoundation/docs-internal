# How to restore the IBC client

* Read about the IBC client and the update process first here: https://ibc.cosmos.network/main/ibc/proposals.html

* Set the environment variables (update corresponding to the chain you work with)

```
export COREUM_CHAIN_ID="{Chain ID}"

export COREUM_CHAIN_ID_ARGS="--chain-id=$COREUM_CHAIN_ID"
export COREUM_NODE_ARGS="--node=$COREUM_NODE"
```

* Create a new IBC client.
  Here we use the `hermes` relayer to do it, the client settings must be the same as before.

```bash
hermes create client --host-chain $COREUM_CHAIN_ID --reference-chain $REFERENCE_CHAIN_ID
```

The `host-chain` is the chain on which the client is expired/frozen now, the `reference-chain` is chain which is
connected to the `host-chain`. In that sample we restore the client on the coreum, but if the coreum client is expired
the process should be reversed.

Make sure to also specify the following flags and the values should be exactly the same as on the expired client:

`--clock-drift <CLOCK_DRIFT>` - The maximum allowed clock drift for this client.
`--trust-threshold <TRUST_THRESHOLD>` - Override the trust threshold specified in the configuration.
`--trusting-period <TRUSTING_PERIOD>` - Override the trusting period specified in the config.

* Query all states and find channels you want to use

```bash
cored q ibc client states $COREUM_CHAIN_ID_ARGS
```

* Set env variables for the clients

```bash
export EXPIRED_CLIENT_ID={Expired client}
export ACTIVE_CLIENT_ID={Active client}
```

* Query status of the clients

```
export COREUM_NODE_REST_URL=*url_for_coreum_node_rest_api*
curl -X GET "https://$COREUM_NODE_REST_URL/ibc/core/client/v1/client_status/$EXPIRED_CLIENT_ID" -H  "accept: application/json"
curl -X GET "https://$COREUM_NODE_REST_URL/ibc/core/client/v1/client_status/$ACTIVE_CLIENT_ID" -H  "accept: application/json"
```

The expected statuses:

status: Expired - for $EXPIRED_CLIENT_ID
status: Active - for $ACTIVE_CLIENT_ID

* Create proposal to upgrade the client (call on the coreum)

```
DEPOSIT=$(cored q gov params --output json $COREUM_CHAIN_ID_ARGS $COREUM_NODE_ARGS |  jq -r '.deposit_params.min_deposit[0]')
DEPOSIT_COIN=$(jq -r '.amount' <<< $DEPOSIT)$(jq -r '.denom' <<< $DEPOSIT)
cored tx gov submit-proposal update-client $EXPIRED_CLIENT_ID $ACTIVE_CLIENT_ID --title Title --description Description \
    --deposit $DEPOSIT_COIN --from your-account -y -b block $COREUM_CHAIN_ID_ARGS $COREUM_NODE_ARGS
```

* Wait for the voting to pass
