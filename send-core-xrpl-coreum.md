# How to send test CORE tokens using Multichain bridge from XRP Ledger to Coreum

There are a couple of steps you need to follow to move your test `CORE`  from `XRP Ledger` wallet to `Coreum` blockchain.

## Resources to be utilized:
* [Multichain UI](https://test.multichain.org/#/router)
* Address with CORE:	`rawkFVa2z24BPDhnxyTjkPdhrFzm23YPwj`
             secret:	`ssAsPVpSjXkwA3EyuU515TBiDM9Tx`
* CORE Token details:
              issuer: `raSEP47QAwU6jsZU493znUD2iGNHDQEyvA`
            currency: `434F524500000000000000000000000000000000`
* [XRPL testnet explorer](https://test.bithomp.com/)
* [UI for issuing custom XRPL txs](https://ripplerm.github.io/ripple-wallet/)
* [Coreum Testnet Block Explorer](https://explorer.testnet-1.coreum.dev/coreum/)


### Generate memo on multichain router page

1. Go to **Multichain UI** page
2. Click on network button at top right corner of the page and search for `XRP Ledger testnet `
3. Enter Coreum testnet wallet address you want the funds to be transferred to into `Wallet Address` field as well as amount into `From` field
4. Click `Swap` button
5. Save values of `Deposit Address` ( this is a multichain bridge address) and `Memo`. They will be needed in the next section

### Issue custom XRPL tx
6. Go to **UI for issuing custom XRPL txs**
7. Selet `TEST`  network
8. Click `Change` button to load a wallet
9. Click use secretkey 
10. Enter the secret key `ssAsPVpSjXkwA3EyuU515TBiDM9Tx` and click `Submit`. Click `Submit` once again.
11. Verify that wallet account address shown is now rawkFVa2z24BPDhnxyTjkPdhrFzm23YPwj 
12. Click on `Payment` tab and check `Advanced mode`
13. Enter `Deposit Address` into `Recipient` field
14. Enter amount (i.e. 10) to transfer into `Value` field
15. Enter **CORE Token issuer** value into `Issuer` field
16. Enter **CORE Token currency**  into `Currency` field
17. Select 0% slippage in `Slippage` field
18. Select proper (the one on the right) value for `Paths search` field
19. Click `Add new`  button under `Memos` section
20. Enter `Memo` into `Memo Data` field and click Submit 
21. Submit the transaction with `Submit`  button
22. Verify success notification is shown

### Verify tx on Testnet block explorer page
23. Enter your testnet core address on **Coreum Testnet Block Explorer** page
24. Verify the transaction you have just executed is present on the list
25. Verify transaction details and updated account balance
