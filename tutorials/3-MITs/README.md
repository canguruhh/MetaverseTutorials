# Tutorial 3 - Metaverse Identifiable Token (MIT)

In this tutorial you will learn how to

* What a MIT is
* how to customize and issue an MIT from your avatar
* Transfer MITs programatically
* Verify a hash stored in the MITs content
* Integrate MITs into your app


## Introduction

MIT stands for **Metaverse Identifiable Token**. MIT is Metaverse's non-fungible token standard. You can think of them as single Issue Assets with a unique identifying symbol. MITs can also store a content string.

What are they used for
MITs can be used to:
* represent real world assets
* represent virtual assets
* store data on the Metaverse Blockchain such as cryptographic hashes

MITs can be transferred between Avatars. And like with MST issuance, only Avatars can issue MITs

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

    <h3> Issue MIT </h3>

    <input placeholder = "MIT Symbol" ></input><br>
    <input placeholder = "Content" ></input><br>
    <select>Avatar</select><br>
    <label placeholder = "recipient Address" ></label>

    <h3> Display MITs</h3>

    <table>
      <tr>
        <th>MST</th>
        <th>Issuer</th>
        <th>Symbol</th>
        <th>Content</th>
      </tr>
    </table>

    <h3> Transfer MIT </h3>

    <select>MIT</select><br>
    <input>AVATAR</input><br>
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
let address = wallet.getAddress(1)
let address2 = wallet.getAddress(0)
console.log(address)
console.log(address2)
let recipient_address =  wallet.getAddress(1)
let change_address = wallet.getAddress(1)
let height = await blockchain.height()
let txs = await blockchain.addresses.txs([address])
let utxos = await Metaverse.output.calculateUtxo(txs.transactions, [address]) //Get all utxo
let result = await Metaverse.output.findUtxo(utxos, target, height) //Collect utxo for given target
let tx = await Metaverse.transaction_builder.registerMIT(result.utxo, recipient_address, description, symbol, content, change_address, result.change)
tx = await wallet.sign(tx)
tx = await tx.encode()
tx = await blockchain.transaction.broadcast(tx.toString('hex'))
console.log(tx)
```

Create a function to Transfer an MIT
```
let wallet = await Metaverse.wallet.fromMnemonic(mnemonic, 'testnet')
let address = wallet.getAddress(0)
let height = await blockchain.height()
let txs = await blockchain.addresses.txs(address)
let utxos Metaverse.output.calculateUtxo(txs.transactions, [address])) //Get all utxo
let results = await Promise.all([
                  Metaverse.output.findUtxo(utxos, {}, height),
                  Metaverse.output.filter(utxos, {
                      type: "mit"
                  })
              ])
let tx = await Metaverse.transaction_builder.transferMIT(results[0].utxo.concat(results[1]), "nova", recipient_address, recipient_avatar, "MIT_SUPERNOVA", change_address, results[0].change))
tx = await wallet.sign(tx))
tx = await tx.encode())
tx = await blockchain.transaction.broadcast(tx.toString('hex')))
console.log(tx)
```

Create a function to verify a MIT content hash
```
function getMIT(symbol) {
   return blockchain.MIT.get(symbol)
}
```

## Connect to your dApp

To interact with `metaversejs` in your webapp, you need to reference `metaversejs` in your HTML.
```
<script type="text/javascript" src="/dist/metaverse.min.js"></script>
```

Also reference your `tut3.js` file.

```
<script type="text/javascript" src="tut3.js"></script>
```

Verify that you have connected metaverse to the webapp by opening the browser console and typing "Metaverse". You should see the Metaverse object come up!

Next connect elements to the js functions and youre done!

## What's next?

You have completed the Metaverse Tutorial series. For further information checkout the [examples](https://github.com/antron3000/MetaverseTutorials/tree/master/examples) section or read the official [Metaverse Documentation](https://docs.mvs.org).

Join the Metaverse developer community today and build amazing dApps! 
