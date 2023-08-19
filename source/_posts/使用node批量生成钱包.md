---
title: 使用node.js批量生成钱包
date: 2023-07-23 23:19:00
categories:
  - Linux
tags:
  - node.js
  - Linux
  - 钱包
  - wallet
  - 批量
description: '使用node.js批量生成钱包是我目前所知的最好用的批量生成钱包的方法'
cover: https://s2.loli.net/2023/07/24/lyTcN5xXsu2wbzP.webp
---

## node.js生成钱包代码

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

##  脚本使用

```js
npm install  # node环境安装
node utils/generateWallets    生成n个钱包+助记词
```

## python生成钱包代码

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
count = 1  # 生成地址数量
for i in range(1, count + 1):
    key, checksum_address = get_eth()
    data.append([checksum_address, key])
    print(str(i))

df = pd.DataFrame(data=data, columns=['地址', '私钥'])
df.to_csv("eth.csv", index=False, encoding="utf_8_sig")
```

## python版本生成器的使用说明

安装好需要的库之后，直接运行，运行结果保留在csv文件中， 总体来说 不如node.js的生成钱包代码更优雅。node.js 的代码效率也更高，同时可以直接用助记词保存。大大方便了用户。

## 注意事项

对于助记词、私钥的保管我这里需要强调一下。千万不能存储到网络上，本子记下来藏好。如果是大批量的私钥，你可以加密存储，也可以拆分分别存储。千万别把完整的私钥、助记词放在网上。网盘、云笔记、iCloud等等地方被盗的太多太多。

除了存储，我们平常最好用代码操作，复制私钥、助记词不要完整复制，剩下一部分手打上去，养成这样的习惯。

一定一定要养成上面的两种好习惯，只要本金不丢永远都可以赚钱，本金没了再赚可谓是难上加难！