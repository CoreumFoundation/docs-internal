# Set up tx utils

Install `jq` util if it is not installed yet.

## Init optimize-tx-fee function 

The function re-computes the fee with the initial gas price to make the tx pass in most of the
chains states.

```bash
function optimize-tx-fee {
    file=$1
    # initial gas price 0.0625
    gas_price_numerator=625
    gas_price_denominator=10000
    file_data=$(<$file)
    gas_limit=$(echo $file_data | jq -r '.auth_info.fee.gas_limit')
    fee_amount=$(( $gas_limit * $gas_price_numerator / $gas_price_denominator + 1 ))
    file_data=$(echo $file_data | jq ".auth_info.fee.amount[0].amount = \"$fee_amount\"")
    echo $file_data > $file
}
```

Example of usage

```bash
optimize-tx-fee my-tx.json
```

## Init join-txs function

The function joins the txs messages and optimizes the gas.

```bash
function join-txs {
    left_data=$(<$1)
    right_data=$(<$2)
    result_file=$3
    # join messages
    right_messages=$(echo $right_data | jq '.body.messages')
    left_data=$(echo $left_data | jq ".body.messages += $right_messages")
    # sum gas
    right_gas_limit=$(echo $right_data | jq -r '.auth_info.fee.gas_limit')
    left_gas_limit=$(echo $left_data | jq -r '.auth_info.fee.gas_limit')
    total_gas=$((right_gas_limit + left_gas_limit))
    left_data=$(echo $left_data | jq ".auth_info.fee.gas_limit = \"$total_gas\"")
    # write dile
    echo $left_data > $result_file
    # optimize tx
    optimize-tx-fee $result_file
}
```

Example of usage

```bash
join-txs left-tx.json right-tx.json result-tx.json
```
