# Geth Example

示範如何使用`geth`
1. 啟動`private chain`
2. 建立兩個帳號
3. 讓兩個帳號彼此交易
4. 啟動挖礦，讓礦工驗證交易

---

## 前置準備

準備 `genesis.json`
```json
{
    "config": {
        "chainId": 123456,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "nonce": "0x0000000000000042",
    "difficulty": "0x020",
    "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "coinbase": "0x0000000000000000000000000000000000000000",
    "timestamp": "0x00",
    "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "gasLimit": "0xffffff",
    "alloc": {}
}
```

若要初始化帳號及金額
```json
"alloc": {
  "0x3a5c16f809a28cdda701ce379da1de4f29d39232": {"balance": "100000000"},
  "0x88fafe8cdf5a263150703b0bc3c6c1757294cd65": {"balance": "200000000"},
}
```

建立資料夾以存放區塊鏈資料
```sh
mkdir data
```

---

## 啟動區塊鏈

建立`創世區塊`，並指定區塊鍊儲存資料夾
```sh
geth --datadir data init genesis.json
```

啟動私有鏈，並執行geth console
```sh
geth --datadir data \
--networkid 123456 \
--rpc \
--rpccorsdomain "*" \
--nodiscover \
--rpcapi="db,eth,net,web3,personal" \
console
```

建立`帳號`，並指定`密碼`
```sh
# Account 1
personal.newAccount("13579")

# Account 2
personal.newAccount("24680")
```

查詢
```sh
# 查詢帳號清單
eth.accounts

# 查詢帳號0地址
eth.accounts[0]

# 查詢餘額
eth.getBalance(eth.accounts[0])

# 使用web3語法查詢餘額
web3.fromWei(eth.getBalance(eth.accounts[0]), "ether")

# coinbase帳號
eth.coinbase

# 查詢區塊數量
eth.blockNumber

# 查詢指定block
eth.getBlock(0)
```

挖礦
```sh
# 使用4個thread挖礦
miner.start(4)

# 停止挖礦
miner.stop()
```

>開始挖礦後，`等待數分鐘`再停止挖礦。此時區塊數量應會增加，且coinbase的帳戶餘額應會有以太幣。

再次查詢
```sh
# 區塊數量
eth.blockNumber

# 查詢餘額
web3.fromWei(eth.getBalance(eth.accounts[0]), "ether")
```

交易
```sh
# 解鎖帳號(password: 13579)
personal.unlockAccount(eth.accounts[0])

# 交易(帳號0給帳號1，3個ether)
eth.sendTransaction({from:eth.accounts[0],to:eth.accounts[1],value:web3.toWei(3,"ether")})

# 查詢交易收據(因交易尚未獲得確認，故此時應為null)
eth.getTransactionReceipt('0x61c248c339399397a91b1924b02f9d8971889f097483638ad6fc5c51e5f62933')

# 查詢交易狀況
txpool.status
```

交易後，為了要驗證transaction並將之放進block，需要啟動礦工挖礦
```sh
# 礦工再次挖礦
miner.start(4)

# 停止挖礦
miner.stop()

# 查詢交易狀況
txpool.status

# 查詢帳號1餘額
eth.getBalance(eth.accounts[1])

# 查詢 transaction (需自行替換位址)
eth.getTransaction("0x61c248c339399397a91b1924b02f9d8971889f097483638ad6fc5c51e5f62933")

# 查詢交易收據
eth.getTransactionReceipt('0x61c248c339399397a91b1924b02f9d8971889f097483638ad6fc5c51e5f62933')
```










