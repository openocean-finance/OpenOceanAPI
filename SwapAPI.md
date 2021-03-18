# OpenOcean Swap API



## 1、Overview



### 1.1、 Environment

**Test environment**:  `https://api-pre.openocean.finance`

- Test environment ETH private chain RPC Host： `https://eth-rpc.openocean.finance`

- Test environment BSC private chain RPC Host：` https://bsc-rpc.openocean.finance`

- Test account seed phrase：`lunch filter egg spin swift message light remain deposit inch section river`

**Production environmen**t : `https://ethapi.openocean.finance`

* Production environment BSC nodes reference：`https://docs.binance.org/smart-chain/developer/rpc.html`

### 1.2、ChainID Illustration

| ChainID | Chain                |
| ------- | -------------------- |
| 1       | BTC（not supported） |
| 2       | ETH                  |
| 3       | ONT                  |
| 4       | NEO（not supported） |
| 5       | BSC                  |



## 2、Swap API

### 2.1、Get information of Swap quote

```text
url：/v1/cross/quote
method：GET
```
- **Request：**

| Parameter |  Type  |    Requirement  |Description   |
| :------------: | :----: | :----------: |:---------- |
| inChainId | String | required |Input ChainID, see 0.1ChainID Illustration, value for ChainID is 2|
| inTokenSymbol | String |optional |Input Token Symbol or inTokenAddress|
| inTokenAddress | String |optional |Input Token Address or inTokenSymbol|
| inAccount | String |required |Users account|
| outChainId | String |required |Output ChainID，see 0.1ChainID Illustration，value for ChainID is 2|
| outTokenSymbol | String |optional |Output Token Symbol or outTokenAddress|
| outTokenAddress | String |optional |Output Token address or outTokenSymbol|
| outAccount | String |required |Users account|
| amount | String |required |Input amount, which needs to be formatted according to decimals|

- Format Amount 
  - `1 ETH` => $1 \times 10^{18} = 1000000000000000000$
  - `1 USDT` => $1 \times 10^6 = 1000000$
- **Response:**

```json
{
  "inToken": {
    "symbol": "ETH", // Token Symbol
    "name": "Ethereum", // Token Name
    "address": "0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE", // Token  Address
    "decimals": 18, // Token Decimals, format inAmount
    "chainId": 2, // ChainID
    "account": "0xFbfa1585bE90cE492045E125eab7E151b67D1B22"
  },
  "outToken": {
    "symbol": "USDT",
    "name": "Tether USD",
    "address": "0xdac17f958d2ee523a2206206994597c13d831ec7",
    "decimals": 6, // Token Decimals, format outAmount
    "chainId": 2,
    "account": "0xFbfa1585bE90cE492045E125eab7E151b67D1B22"
  },
  "inAmount": "1000000000000000000", // nput amount，process data according to inToken.decimals 

  "outAmount": "352087055", // Current convertible amount，process data according to outToken.decimals 
  "crossType": 1 // cross-chain type, not important at present
}
```

* **Notice:**

​      The example above indicates the swap price as follows:

![](https://prod-ontrade-oss.oss-cn-shanghai.aliyuncs.com/gitbook/api-image.png)

### 2.2、 Get information of Swap transactions

```
url：/v1/cross/swap-quote
method：GET
```
- **Request：**

| Parameter |  Type  |    Requirement  |Description   |
| :------------: | :----: | :----------: |:---------- |
| inChainId | String | required |Input ChainID, see 0.1ChainID Illustration, value for ChainID is 2|
| inTokenSymbol | String |optional |Input Token Symbol or inTokenAddress|
| inTokenAddress | String |optional |Input Token Address or inTokenSymbol|
| inAccount | String |required |Users account|
| outChainId | String |required |Output ChainID，see 0.1ChainID Illustration，value for ChainID is 2|
| outTokenSymbol | String |optional |Output Token Symbol or outTokenAddress|
| outTokenAddress | String |optional |Output Token address or outTokenSymbol|
| outAccount | String |required |Users account|
| amount | String |required |Input amount|
| slippage | number |optional |slippage，default value:100, 0-10000，1 means0.01%，10000 means100%|

* **Response:**

```json
{
  "inToken": {
    "symbol": "ETH",
    "name": "Ethereum",
    "address": "0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE",
    "decimals": 18,
    "chainId": 2,
    "account": "0xFbfa1585bE90cE492045E125eab7E151b67D1B22"
  },
  "outToken": {
    "symbol": "USDT",
    "name": "Tether USD",
    "address": "0xdac17f958d2ee523a2206206994597c13d831ec7",
    "decimals": 6,
    "chainId": 2,
    "account": "0xFbfa1585bE90cE492045E125eab7E151b67D1B22"
  },
  "inAmount": "1000000000000000000",
  "outAmount": "352087055",
  "crossType": 1,
  "txs": [
    {
      "chainId": 2,
      "tx": {
        "to": "0x6713bD0Ed5d5De63Ac262251feBC2601F2cE95aA",
        "gasLimit": "270000",
        "gasPrice": "55000000000",
        "data": "0x...",
        "value": "1000000000000000000",
        "chainId": 1
      },
      "type": 1
    }
  ]
}
```

* `txs`

`txs is an array of transaction information. In some cases, the “Approve” transaction needs to be performed first to complete the “Swap” transaction. At this time, txs will hold two transactions at the same time. The two transactions need to be signed in sequence and sent to The Ethereum network, and due to the mechanism of Ethereum, the second transaction cannot be executed until the first transaction has been successfully confirmed.`

```json
{
      "chainId": 2, // OpenOcean ChainID
      "tx": { // Ethereum，see Ethereum documents
        "to": "0x6713bD0Ed5d5De63Ac262251feBC2601F2cE95aA", // Execute hash contract 
        "gasLimit": "270000", //  manual increase is recommended to increase the probability of success
        "gasPrice": "55000000000", // manually increasing is recommended to increase the probability of                                           success
        "data": "0x...", // depending on the SDK, the load of executing the contract may need to be                                    converted.see SDK documents
        "value": "1000000000000000000", // this value should be set when ETH is changed to other tokens,                                                please do not ignore or modify
        "chainId": 1 // represent Ethereum mainnet, which is different from ChainID in OpenOcean
      },
        "index": 1, // can be ignored
  		"type": 1 // transaction types，1: Approve；2：cross chain；3：Swap 
}
```

### 2.3、GET supported Token(ETH) list

Get symbol and decimals configuration of all the supported Token。

```
url：/v1/token
method：GET
```

*  **Request**

​      None

-  **Response**

```json
[
  {
    "symbol": "ETH",
    "name": "Ethereum",
    "decimals": 18,
    "address": "0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE"
  },
  {
    "symbol": "USDT",
    "name": "Tether USD",
    "address": "0xdac17f958d2ee523a2206206994597c13d831ec7",
    "decimals": 6
  }
]
```

### 2.4、GET supported Token(BSC) list

```
url：/v1/bsc/token
method：GET
```

* See 2.3
