---
title: Batch Wallet Generation with node.js
date: 2023-07-23 23:19:00
categories:
  - Linux
tags: 
  - node.js
  - node.js
  - wallet
  - wallet
  - batch
description: 'Using node.js to batch generate wallets is the best way to batch generate wallets that I know of'
cover: https://s2.loli.net/2023/07/24/lyTcN5xXsu2wbzP.webp
---

## node.js generate wallet code

```js
import { ethers } from "ethers"
import fs from "fs"

const wallet = ethers.Wallet.createRandom()

const data = {
  mnemonic: wallet.mnemonic.phrase,
  accounts: [],
}

const HDNode = ethers.utils.HDNode.fromMnemonic(wallet.mnemonic.phrase)

for (let i = 0; i < 100; i++) {
  const node = HDNode.derivePath(`m/44'/60'/0'/0/${i}`)
  data.accounts.push({
    pub: node.address,
    pri: node.privateKey,
  })
}

if (!fs.existsSync("accounts.json")) {
  fs.writeFile("./accounts.json", JSON.stringify(data, 0, 4), (err) => {
    if (err) console.error(err)
    else {
      console.log("🤩 100 accounts generated successfully!")
    }
  })
} else {
  console.log(
    "You've already generated 100 accounts, are you sure to generate a new one?\nIf you want please delete accounts.json, and remember to backup the mnemonic first⚠️!!!"
  )
}

```

##  Script Usage

```js
npm install  # node environment installation
node utils/generateWallets    Generate n wallets + mnemonics
```

## python generate wallet code

```python
import blocksmith
import random
import pandas as pd


def get_eth():
    num = random.sample("abcdefghijklmnopqretuvwxyzABCDEFGHIJKLOMOPQRSTUVWXYZ$%^$@%^&*@^(%rs0123456789!", 50)
    strs = ''
    seed = strs.join(num)
    kg = blocksmith.KeyGenerator()
    kg.seed_input(seed)
    key = kg.generate_key()
    # print(key)

    address = blocksmith.EthereumWallet.generate_address(key)
    checksum_address = blocksmith.EthereumWallet.checksum_address(address)
    # print(checksum_address)

    return key, checksum_address


data = []
count = 1  # of addresses generated
for i in range(1, count + 1):
    key, checksum_address = get_eth()
    data.append([checksum_address, key])
    print(str(i))

df = pd.DataFrame(data=data, columns=['地址', '私钥'])
df.to_csv("eth.csv", index=False, encoding="utf_8_sig")
```

## Instructions for python version generator

After installing the required libraries, run it directly, and the result is kept in the csv file. Overall It is not as elegant as the generated wallet code of node.js. The code of node.js is also more efficient, and at the same time, it can be saved directly with auxiliary words. This is very convenient for users.

## Notes

I need to emphasize the storage of mnemonic and private key. Never store them on the network, write them down and hide them. If it is a large number of private keys, you can encrypt the storage, you can also split the separate storage. Never put the complete private key, mnemonic on the internet. Too many of them are stolen from places like Netflix, Cloud Notes, iCloud, and so on.

In addition to storage, we usually best to use the code operation, copy the private key, mnemonic words do not complete copy, the remaining part of the hand-typed up, to develop such a habit.

Must must develop the above two good habits, as long as the principal is not lost can always make money, the principal is gone and then earn can be difficult!
