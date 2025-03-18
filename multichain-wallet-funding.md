The flow to fund Multichain wallets to minimize risk.

1. We must create a wallet on XRPL and share the address with Multichain team. 
2. Multichain will create their wallet on Coreum to serve as the funding pool.
3. We will fund the Multichain account on coreum with initial 5m funds.
4. Multichain will observe their own wallet on Coreum ask for funds, once is runs below 1m. We should also
do the same and observe that wallet and not rely only on Multichain.
5. To transfer funds to Multichain wallet, we will first check our wallet on XRPL, make sure that
the values present in the wallet is almost equal to the values absent from Coreum wallet. Then burn some
tokens in XRPL and make exact value transfers in Coreum.
This will help reduce the risk. If the bridge is hacked, we will know it by observing that the missing tokens
in coreum wallet is more than the tokens present in XRPL wallet.
6. To reduce the amount that we have lent to Multichain we just need to burn that amount on XRPL wallet and don't
make the counterpart transcation on coreum side.
