# Tutorial 2 - Avatars and Metaverse Smart Tokens (MST)

In this tutorial you will learn how to

* Register an Avatar
* Verify an Avatar against an address
* Customize and issue an MST from your Avatar
* Check your MST balances
* Transfer MSTs programatically
* Integrate Avatar and MST functionality into your app


## Introduction

**Avatars** are digital representations of personal identities -- **digital identitites (DIDs)** -- that exist on the Metaverse Blockchain.
Avatars have unique alphanumeric symbols. An Avatar is attached to exactly one Metaverse address (i.e there can only be at most one Avatar per address).
Transfer of tokens can be done by referring to Avatars instead of addresses.

For more info on Avatars, visit this [article](https://medium.com/metaverse-blockchain/metaverse-explained-avatars-57be355d42d4).

**Metaverse Smart Token (MST)** is Metaverse's fungible token standard. MSTs can be seen as sub-currencies on the Metaverse Blockchain.
It costs 10 ETP to create an MST. The creation and issuance of an MST must be done through an Avatar (i.e. to issue a token, you can't just use a Metaverse address that's not already tied to an Avatar).
Again, ETP and other Metaverse Smart Tokens (MSTs) can be sent to Avatars instead of addresses.

There are other things an Avatar can or cannot do and such limitations are defined through a system of **Certificates**. There are three types of certificates on Metaverse: Domain Certificate, Secondary Issue Certificate and Naming Certificate. For more details on what they do, check out this [article](https://medium.com/metaverse-blockchain/metaverse-explained-certificates-b84767d1ae8f).

## Hands-on Tutorial

First let's create an html front-end
```
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title></title>
  </head>
  <body>

    <h3> Register Avatar </h3>

    <input placeholder="Avatar Name"></input>
    <label>Avatar Address</label> <select></select>
    <button>Register</button>
    
    <h3> Issue MST </h3>

    <input placeholder="symbol"></input> <br>
    <input placeholder="max_supply"></input>
    <button>Issue</button>

    <h3> Balances </h3>
    
    <table>
      <tr>
        <th>MST</th>
        <th>Issuer</th>
        <th>Amount</th>
      </tr>
    </table>

    <h3> Send MST </h3>

    <label></label><select></select>
    <input placeholder ="sendTo"></input><br>
    <input placeholder = "amount"></input><br>

  </body>
</html>
```

Now, create a function to register your Avatar. Remember, you'll have to initialize the `wallet` object first (see the previous tutorial).
```
async function registerAvatar(avatar_name,avatar_address) {

    let change_address = avatar_address
    let height = await blockchain.height()
    let txs = await blockchain.addresses.txs([avatar_address])
    let utxos = await Metaverse.output.calculateUtxo(txs.transactions, [avatar_address]) //Get all utxo for the avatar address
    let result = await Metaverse.output.findUtxo(utxos, {}, height, 100000000) //Collect utxo to pay for the fee of 1 ETP
    let tx = await Metaverse.transaction_builder.issueDid(result.utxo, avatar_address, avatar_name, change_address, result.change, 80000000, 'testnet')
    tx = await wallet.sign(tx)
    tx = await tx.encode()
    tx = await tx.toString('hex')
    tx = await blockchain.transaction.broadcast(tx)
}
```

Create a function to look up an avatar address

```
function getAvatar(avatar) {
  return blockchain.avatar.get(avatar);
}
```


Create a function to issue an MST

```
async function issueMST(issuer,symbol,max_supply,decimalPrecision,issuer,description){

  var recipient_address = issuer;
  var change_address = issuer;

  let wallet = await Metaverse.wallet.fromMnemonic("lunar there win define minor shadow damage lounge bitter abstract sail alcohol yellow left lift vapor tourist rent gloom sustain gym dry congress zero")
  let txs = await blockchain.addresses.txs(wallet.getAddresses())
  let utxos = await Metaverse.transaction_builder.calculateUtxo(txs.transactions, wallet.getAddresses()) //Get all utxo
  let result = await Metaverse.transaction_builder.findUtxo(utxos, {}, Metaverse.transaction.ASSET_ISSUE_DEFAULT_FEE) //Collect utxo for given target
  let tx = await Metaverse.transaction_builder.issue(result.utxo, recipient_address, symbol, max_supply, precision, issuer, description, change_address, result.change)

  tx = await wallet.sign(tx)
  tx = await tx.encode()
  console.log(tx.toString('hex'));

}
```

Create a function to get your MST balance
```
async function getBalances(){
  let wallet = await Metaverse.wallet.fromMnemonic(mnemonic, 'testnet')
  let height = await blockchain.height()
  let addresses = wallet.getAddresses()
  let txs = await blockchain.addresses.txs(addresses)

  let utxo = await Metaverse.output.calculateUtxo(txs.transactions, wallet.getAddresses())
  let balances = await blockchain.balance.all(utxo, wallet.getAddresses(), height)
  console.log(balances.MST)
}
```


Create a function to transfer MSTs
```
async function transferMST() {
  let height = await blockchain.height()
  let txs = await blockchain.addresses.txs(["tDZ5YMLJ3z6VbvAsX1c8oe9hJ2nND4jszz", "t85Hm2nYwQXrry2cVmEHPq8krRdJ7KYjmq"])
  let utxos = await Metaverse.output.calculateUtxo(txs.transactions, ["tDZ5YMLJ3z6VbvAsX1c8oe9hJ2nND4jszz", "t85Hm2nYwQXrry2cVmEHPq8krRdJ7KYjmq"])) //Get all utxo
  let result = await Metaverse.output.findUtxo(utxos, target, height)) //Collect utxo for given target
  let tx = await Metaverse.transaction_builder.send(result.utxo, recipient_address, undefined, target, change_address, result.change))
  tx = await wallet.sign(tx))
  tx = await tx.encode())
  tx = await blockchain.transaction.broadcast(tx.toString('hex')))
}
```

## Connect to your dApp

To interact with `metaversejs` in your webapp, you need to reference `metaversejs` in your HTML.
```
<script type="text/javascript" src="/dist/metaverse.min.js"></script>
```

Also reference your `tut2.js` file.
```
<script type="text/javascript" src="tut2.js"></script>
```

Verify that you have connected metaverse to the webapp by opening the browser console and typing "Metaverse". You should see the Metaverse object come up.

Next connect elements to the js functions and you're done!

## What's Next?
Continue with the next tutorial and learn how to work with [Metaverse Identifiable Token (MIT)](../3-MITs)
