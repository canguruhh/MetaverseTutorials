# Tutorial 3 - Metaverse Identifiable Token (MIT)

In this tutorial you will learn

* What an MIT is
* How to customize and issue an MIT from your Avatar
* How to transfer MITs programatically
* How to verify a hash stored in the MITs content
* How to integrate MITs into your app


## Introduction

MIT stands for **Metaverse Identifiable Token**. MIT is Metaverse's non-fungible token standard. You can think of it as a single-issue asset with a unique identifying symbol. An MIT can also store a content string.

MITs can be used to
* Represent real world assets
* Represent virtual assets
* Store data on the Metaverse Blockchain such as cryptographic hashes

MITs can be transferred between Avatars. Just like with MST issuance, only Avatars can issue MITs.

## Hands-on Tutorial

First let's create an html front-end
```
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title>Tutorial 3</title>
  </head>
  <body onload="initialize()">

    <h3> Issue MIT </h3>
    <input placeholder = "MIT Symbol" ></input><br>
    <input  placeholder = "Content" ></input><br>
    <label>Issuing Avatar: </label><select id="avatarSelect"></select><br>
    <label placeholder = "recipient Address" ></label>
    <button >Issue</button>

    <h3> Display MITs</h3>
    <table>
      <tr>
        <th>Symbol</th>
        <th>Content</th>
        <th>Owner</th>
        <th>Address</th>
        <th>Status</th>
      </tr>
    </table>

    <h3> Transfer MIT </h3>
    <label>MIT: </label><select >MIT</select><br>
    <inputplaceholder = "send To...."></input><br>
    <button>Send</button><br>

    <h3> Verify File </h3>
    <label>upload</label><br>
    <select>MIT</select><br>
    <button>Verify</button>

  </body>
</html>
```

Create a function to issue an MIT
```
async function issueMIT(symbol, content,issuer_avatar){
  let target = {
    ETP: 10000
  };
  let issuingAddress = await getAvatar(issuer_avatar)
  issuingAddress = issuingAddress.toString()
  let recipient_address = issuingAddress;
  let change_address = issuingAddress;
  let height = await blockchain.height()
  let txs = await blockchain.addresses.txs(wallet.getAddresses())
  let utxos = await Metaverse.output.calculateUtxo(txs.transactions, [recipient_address]) //Get all utxo
  let result = await Metaverse.output.findUtxo(utxos, {}, height,10000) //Collect utxo to pay fee of 0.0001 ETP
  let tx = await Metaverse.transaction_builder.registerMIT(result.utxo, recipient_address, issuer_avatar, symbol, content, change_address, result.change)
  tx = await wallet.sign(tx)
  tx = await tx.encode()
  tx = await blockchain.transaction.broadcast(tx.toString('hex'))
  console.log(tx)
}
```

Create a function to get MIT data. This will be a list of all MIT's owned by avatars in your mnemonic generated wallet
```
async function getMITData(addressArray){
  //Get the lastest Blockchain Length
  let height = await blockchain.height()

  //Get a list of wallet transactions
  let txs = await blockchain.addresses.txs(addressArray)

  //Get a list of unspent transaction outputs amongst your transactions
  let utxo = await Metaverse.output.calculateUtxo(txs.transactions, addressArray)

  //Calculate your balances based on the utxos
  let balances = await blockchain.balance.all(utxo, addressArray, height)

  let MITData = balances.MIT
  return MITData
}
```

Create a function to verify an MIT content hash
```
function getMIT(symbol) {
   return blockchain.MIT.get(symbol)
}
```

Create a function to transfer an MIT
```
async function sendMIT(symbol,MITIndex,recipient_avatar){
  let symbol = MITData[MITIndex].symbol
  let recipient_address = await getAvatar(recipient_avatar)
  let sender_avatar = MITData[MITIndex].owner
  let sender_address = await getAvatar(sender_avatar)
  let change_address = sender_address;
  console.log(recipient_address)
  console.log(symbol)
  console.log(sender_address)
  let height = await blockchain.height()
  let txs = await blockchain.addresses.txs(addresses)
  let utxos = await Metaverse.output.calculateUtxo(txs.transactions, [sender_address]) //Get all utxo
  //console.log(utxos)
  let results = await Promise.all([
                    Metaverse.output.findUtxo(utxos, {}, height),
                    Metaverse.output.filter(utxos, {
                        symbol: symbol
                    })
                ])
  let tx = await Metaverse.transaction_builder.transferMIT(results[0].utxo.concat(results[1]), sender_avatar, recipient_address, recipient_avatar, symbol, change_address, results[0].change)
  tx = await wallet.sign(tx)
  tx = await tx.encode()
  tx = await blockchain.transaction.broadcast(tx.toString('hex'))
  console.log(tx)
}
```

## Connect to Your DApp

To interact with `metaversejs` in your web app, you need to reference `metaversejs` in your HTML.
```
<script type="text/javascript" src="/dist/metaverse.min.js"></script>
```

Also reference your `tut3.js` file.

```
<script type="text/javascript" src="tut3.js"></script>
```

Verify that you have connected metaverse to the web app by opening the browser console and typing "Metaverse". You should see the Metaverse object come up!

Next connect elements to the js functions and you're done!

## What's Next?

You have completed the Metaverse Tutorial series. For further information, check out the [examples](https://github.com/mvs-org/MetaverseTutorials/tree/master/examples) section or read the official [Metaverse Documentation](https://docs.mvs.org).

Join the Metaverse developer community today and build amazing dApps! 
