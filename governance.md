# Governance at Cosmos SDK v0.47
Cosmos SDK v0.47 introduced a new way of proposals. 

To create a proposal you should do the following steps:
1. Create a draft proposal file:
    ```bash
    cored tx gov draft-proposal
    ```
    It will lead you through in interactive way. 
    * P.S. Don't change account authority - it will differ from your account bank address.
      To get the account's authority:
    ```bash
    cored q auth module-account gov
    ```
    
    After completion, you will find two files - `draft_proposal.json` and `draft_metadata.json`.
    [//]: # (TODO: add info about metadata)
    Artem S. managed to change MaxValidators without metadata with the following proposal:
    ```json
    {
     "messages": [
      {
       "@type": "/cosmos.staking.v1beta1.MsgUpdateParams",
       "authority": "devcore10d07y265gmmuvt4z0w9aw880jnsr700jmjxul5",
       "params": {
        "unbonding_time": "604800s",
        "max_validators": 64,
        "max_entries": 7,
        "historical_entries": 10000,
        "bond_denom": "udevcore",
        "min_commission_rate": "0.000000000000000000"
       }
      }
     ],
     "deposit": "1000udevcore",
     "title": "Update params",
     "summary": "We need to change staking params"
    }
    ```
   I chose `/cosmos.slashing.v1beta1.MsgUpdateParams` proposal type for that out of the list.
   P.S. You should provide the whole `params` object to pass a validation.

2. Submit a proposal:
    ```bash
    cored tx gov submit-proposal --from devcore1u6dycnl606n95ggeatusc3zlfd5m4xqpw66et4 $CHAIN_ARGS ./draft_proposal.json
    ```
   
3. Vote:
    ```bash
    cored tx gov vote 1 yes --from devcore1suzrkpevl84y0s98rdzqchrjx9vva5evtjklhd $CHAIN_ARGS
    ```

P.S. If you face difficulties with new proposal mode, maybe `cored tx gov submit-legacy-proposal` will work for you.
