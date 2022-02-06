# Create a Token on Cardano

This tutorial was executed on Ubuntu via Windows Subsystem for Linux (WSL), following the steps of Cardano Documentation (https://developers.cardano.org/docs/native-tokens/minting/)

Prerequisites:
* Cardano-node and cardano-cli intalled
* Cardano-node running and synced

## Cardano node socket path
You need to set the variable to the corresponding path of the --socket-path parameter:
```shell
export CARDANO_NODE_SOCKET_PATH="$HOME/TESTNET_NODE/socket/node.socket"
```

## Improve readability
Use the xxd tool to encode the token names:
```shell
tokenname1=$(echo -n "Testtoken" | xxd -b -ps -c 80 | tr -d '\n')
tokenname2=$(echo -n "SecondTesttoken" | xxd -b -ps -c 80 | tr -d '\n')
tokenamount="10000000"
output="0"
```

## Check your node status
to check if our Node is up to date compare it to the current value displayed in the Cardano Explorer for the testnet.

```shell
cardano-cli query tip --testnet-magic 1097911063
```
Should give you an output like this:
```json
{
    "epoch": 282,
    "hash": "82cfbbadaaec1a6204442b91de1535505b6482ae9858f3f0bd9c4bb9c8a2c12b",
    "slot": 36723570,
    "block": 6078639,
    "era": "Mary"
}
```

Epoch and slot number should match when being compared to the [Cardano Explorer for testnet](https://explorer.cardano-testnet.iohkdev.io/en.html)

## Set up your workspace
Make a new directory to start with a clean slate
```shell
mkdir tokens
cd tokens/
```

## Generate keys and address
If you already have a payment address and keys and you want to use those, you can skip this step.
If not - we need to generate those to submit transactions and to send and receive ada or native assets.

Payment verification and signing keys are the first keys we need to create.
```shell
cardano-cli address key-gen --verification-key-file payment.vkey --signing-key-file payment.skey
```

Those two keys can now be used to generate an address.
```shell
cardano-cli address build --payment-verification-key-file payment.vkey --out-file payment.addr --testnet-magic 1097911063
```

We will save our address hash in a variable called `address`.
```shell
address=$(cat payment.addr)
```

## Fund the address
Submitting transactions always require you to pay a fee. Sending native assets requires also requires sending at least 1 ada.
So make sure the address you are going to use as the input for the minting transaction has sufficient funds.

For the testnet, you can request funds through the [testnet faucet](https://developers.cardano.org/docs/integrate-cardano/testnet-faucet/).

## Export protocol parameters
For our transaction calculations, we need some of the current protocol parameters. The parameters can be saved in a file called protocol.json with this command:
```shell
cardano-cli query protocol-parameters --testnet-magic 1097911063 --out-file protocol.json
```

## Minting native assets
We'll make a separate sub-directory in our work directory to keep everything policy-wise separated and more organized. For further reading, please check the official docs or the github page about multi-signature scripts.
```shell
mkdir policy
```

First of all, we — again — need some key pairs:
```shell
cardano-cli address key-gen \
    --verification-key-file policy/policy.vkey \
    --signing-key-file policy/policy.skey
```

Create a `policy.script` file and fill it with an empty string.
```shell
touch policy/policy.script && echo "" > policy/policy.script
```

Use the `echo` command to populate the file:
```shell
echo "{" >> policy/policy.script 
echo "  \"keyHash\": \"$(cardano-cli address key-hash --payment-verification-key-file policy/policy.vkey)\"," >> policy/policy.script 
echo "  \"type\": \"sig\"" >> policy/policy.script 
echo "}" >> policy/policy.script
```

## Asset minting
To mint the native assets, we need to generate the policy ID from the script file we created.
```shell
cardano-cli transaction policyid --script-file ./policy/policy.script > policy/policyID
```

The output gets saved to the file `policyID` as we need to reference it later on.

Before we start, we will again need some setup to make the transaction building easier. First, query your payment address and take note of the different values present.
```shell
cardano-cli query utxo --address $address --testnet-magic 1097911063
```

Your output should look something like this (fictional example):
```
                           TxHash                                 TxIx        Amount
--------------------------------------------------------------------------------------
b35a4ba9ef3ce21adcd6879d08553642224304704d206c74d3ffb3e6eed3ca28     0        1000000000 lovelace
```

Since we need each of those values in our transaction, we will store them individually in a corresponding variable.
```shell
txhash="insert your txhash here"
txix="insert your TxIx here"
funds="insert Amount here"
policyid=$(cat policy/policyID)
fee="300000"
```

Now we are ready to build the first transaction to calculate our fee and save it in a file called matx.raw:
```shell
cardano-cli transaction build-raw \
 --fee $fee \
 --tx-in $txhash#$txix \
 --tx-out $address+$output+"$tokenamount $policyid.$tokenname1 + $tokenamount $policyid.$tokenname2" \
 --mint="$tokenamount $policyid.$tokenname1 + $tokenamount $policyid.$tokenname2" \
 --minting-script-file policy/policy.script \
 --out-file matx.raw
```

We now have every value we need to re-build the transaction, ready to be signed. So we reissue the same command to re-build, the only difference being our variables now holding the correct values.
```shell
cardano-cli transaction build-raw \
--fee $fee  \
--tx-in $txhash#$txix  \
--tx-out $address+$output+"$tokenamount $policyid.$tokenname1 + $tokenamount $policyid.$tokenname2" \
--mint="$tokenamount $policyid.$tokenname1 + $tokenamount $policyid.$tokenname2" \
--minting-script-file policy/policy.script \
--out-file matx.raw
```

Transactions need to be signed to prove the authenticity and ownership of the policy key.
```shell
cardano-cli transaction submit --tx-file matx.signed --testnet-magic 1097911063
```

🎉🎉🎉  Congratulations, we have now successfully minted our own token. 🎉🎉🎉 