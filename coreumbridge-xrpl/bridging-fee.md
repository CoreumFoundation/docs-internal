# Token bridging fee

The doc explains the token bridging fee value.

## Fee calculation

```
save_evidence_gas = 150k - 300k (depending on evidence type and contract state | first costs less than next)
save_signature_gas = 250k - 440k (first costs less than next)
relayers_count = 32
gas_price = 0.0625ucore (we take the initial gas price for the calculation)
```

The coreum amount spent from XRPL to Coreum will be less since the relayer needs to provide the evidence only, that's
why we calculate the worst case Coreum to XRPL.

```
min_coreum_bridging_fee = (save_evidence_gas + save_signature_gas) * relayers_count * gas_price
min_coreum_bridging_fee = ((300000 + 440000) * 32 * 0.0625) = 1480000ucore = 1.48core
```

Now we can calculate the `token` bridging fee.

```
min_coreum_bridging_fee = 1.48core
core_usd_price = 0.15
token_usd_price = 0.64 (for example XRP )
# price_multiplier includes:
## tokens price volatility
## technical transactions cost (tickets allocation, keys rotation and etc)
## additional value to make relaying profitable for relayers 
price_multiplier = 2.5 

bridging_fee = min_coreum_bridging_fee * price_multiplier * core_usd_price / token_usd_price
bridging_fee = 1.48 * 2.5 * 0.15 / 0.64 ~= 0.87 (XRP) or ~= 0.56$
```

Fee paid on XRPL is negligible, hence we don't consider it.