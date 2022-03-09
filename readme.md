# Go-Ethereum

## Basic Introduction
Go-Ethereum，又簡稱Geth，為：
1. Ethereum Protocal
2. Command Line 工具

當我們運行了Geth以後：
1. Geth會以區塊鏈節點的方式運行
2. Geth會找到其他區塊鏈節點並與其連接
3. Geth會在本地端打開一JavaScript RPC，讓我們可以以此和節點互動
4. 開始連接節點，下載（複製）區塊鏈上的資料
5. 區塊鏈（資料）會存在我們的本地端，並不存在任何的中央伺服器或者資料庫

Geth是如何同步於區塊鏈的：
1. Full SYNC
    * 最為緩慢，同步過會永遠進行，理論上沒有停止的一天
    * 複製內容包含Block Header + Block Bodies等，為全部的歷史交易資料
    * 總資料量大小超過35GB
2. Fast SYNC(Default)
    * 複製時間大約為幾個小時至一天，
    * 複製內容包含所有區塊的Block Header + Block Bodies，但只同步最新的1024筆交易
3. Light SYNC(Experimental)
    * 同步時間僅需幾分鐘，資料大小約幾百MB
    * 複製內容只有Block Header，只同步最新的快取
    * 安全性較低但速度非常快，屬於實驗性質的同步方式


## Overview

| Name | Statement |
| -------- | -------- |
| Private Network| 私有網路會獨立於其他以太坊網路|
| Node|區塊鏈網路的參與者|
|Bootnode||幫助節點尋找 P2P 網路中的其他節點|
|Miner|於區塊裡打包交易的礦工|
|Account|使用公私鑰與區塊鏈網路進行互動|
|Genesis Configuration|是 Geth 工具用來創建創世區塊以及區塊鏈的配置文件，要注意的是 genesis.json 並不是創世區塊本身。|

不同的區塊鏈性質
* 公鏈（Public Blockchain）：任何人都可以平等地參與、挖礦、讀取鏈上資訊、進行交易，例如：以太坊主網和測試網。
* 聯盟鏈（Consortium Blockchain）：任何經過授權的節點都可以參與。
* 私有鏈（Private Blockchain）：只有鏈的擁有者可以參與、挖礦、讀取鏈上資訊、進行交易。

### 下載 Geth
Windows 在這裡下載並選擇相對應的版本。 https://geth.ethereum.org/downloads/#
使用以下指令測試是否安裝成功。
```
geth -h
```
MacOSX 也可以直接使用：
```
brew tap ethereum/ethereum
brew install ethereum

geth --help
```

如果直接不帶任何參數的打開Geth：
`$ geth`
1. 開啟一個P2P節點
2. 下載所有的區塊資料到本地端電腦（每個區塊大約2MB）
3. 同步模式預設為Fast Sync
4. 若需要中斷區塊同步可使用ctrl+c

### Geth 的Log-Output
`INFO [02-19|00:18:12.450] Starting peer-to-peer node               instance=Geth/v1.10.14-stable-11a3a350/windows-amd64/go1.17.5` <br>
* Geth並不具有自動更新的機制，所以我們需要時時刻刻的手動將其更新到最新版本
* Ethereum Protocol和Geth有密不可分的關係
* bits-version有32和64

`INFO [02-19|00:18:12.417] Initialised chain configuration          config="{ChainID: 1 Homestead: 1150000 DAO: 1920000 DAOSupport: true EIP150: 2463000 EIP155: 2675000 EIP158: 2675000 Byzantium: 4370000 Constantinople: 7280000 Petersburg: 7280000 Istanbul: 9069000, Muir Glacier: 9200000, Berlin: 12244000, London: 12965000, Arrow Glacier: 13773000, MergeFork: <nil>, Engine: ethash}"`<br>
* Configuration
* chainID

`INFO [02-19|00:18:12.472] IPC endpoint opened                      url=\\.\pipe\geth.ipc`<br>
* Windows系統為pipe；Linux&Mac系統需要透過檔案傳送IPC
* 這裡並沒有HTTP RPC endpoint open(loclahost:8545)，所以我們無法在此直接連接瀏覽器

### 資料預設儲存位置
* 我們可以在此找到所有的區塊資料：User->AppData->Roaming->Ethereum->geth->chaindata
* 我們可以在此找到所有的密鑰資料：User->AppData->Roaming->Ethereum->geth->keystore
> Linux: ~/.ethereum 
> OSX: ~/Library/Ethereum 
> Windows: ~/AppData/Roaming/Ethereum

### Attach Geth
如果只輸入$ geth 作為命令，將會作為伺服器一樣的與外界連接。當我們先以 $ geth的方式啟動，再新開一個命令提示字元以以下方式啟動Geth，便可將新開的視窗做為客戶端：
```
$ geth attach
```
    
使用 JavaScript JSON RPC API
```
$ geth attach
// look for the module
$ admin
$ web3
$ eth
```
https://github.com/ethereum/go-ethereum/wiki/Management-APIs

Genesis.json Files
```
建立創世區塊的「資料檔」：genesis.json，此僅是定義和標明，並非創世區塊本身
{
    "config": {
        "chainID": 15,
        "homesteadBlock": 0,
        "eip150Block": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "difficulty": "0x20000",
    "gasLimit": "0x0000008",
    "alloc": {}
}
```


|參數|介紹|
| -------- | -------- |
|chainID|這裡和等下的 networkid 一樣由 EIP 155 提供 chainid values 的建議來代稱不同網路。只有當 network、chainID、創世區塊配置都相同時，才是同一條鏈。|
|homesteadBlock|0 為使用 ethereum homestead 版本。Homestead 是以太坊的第二個主要版本。和 2017 以太坊的拜占庭硬分叉有關|
|eip150Block|設為 0 表示私有鏈不會因為 EIP-150 這個提議而分岔|
|eip155Block|設為 0 表示私有鏈不會因為 EIP-155 這個提議而分岔|
|eip158Block|設為 0 表示私有鏈不會因為 EIP-158 這個提議而分岔|
|nonce|用於挖礦的 64bits 隨機數，是與 PoW 機制有關的值|
|difficulty|定義了每次挖礦時，最終確定nonce 的難度。詳細內容可見 Ethash(PoW) 方法|
|mixhash|與 nonce 配合用於挖礦，由上一個區塊的部分產生雜湊值|
|coinbase|預設為第一個建立挖礦的礦工|
|timestamp|創世區塊的時間戳|
|parentHash|指定了本區塊的上一個區塊Hash，因此創世區塊的 parentHash 是 0|
|extraData|附加訊息。若是在 Clique(PoA) 機制下，新區塊只能被簽名人（singers）挖掘，區塊鏈生長過程中，可以通過投票來選舉或者免除簽名人。在區塊鏈開始運行時，需要定義一個初始 singer。 詳見 EIP-225。|
|gasLimit|規定該區塊鏈中，交易使用的 gas 的總量上限。某種程度上是規定區塊中能包含的交易信息量總和。|
|alloc|可以從創世區塊預置帳號及其帳號內的金額，單位是 wei 不是 eth|

> EIPs 是以太坊中的一套標準，內容會包含 core protocol specifications、用戶 API、和合約標準。
> gas 是一個在與合約進行交易時的內部價格，當我們傳送一些指令給 Ethereum Virtual Machine (EVM) 來進行交易或與合約互動時就會消耗一定量的 gas。
Private Network中使用JSON API
    
打開一PowerShell作為伺服器端
```
$ geth --datadir .\privateChaindata\ --nodiscover
```
    
打開另一個PowerShell作為客戶端
```
$ geth attach ipc:\\.\pipe\geth.ipc
< 
Wellcome to the Geth Javascript console!
```
          
Attach Javascript Console
在這個交互式的 Javascript 執行環境中，> 是命令提示符。在這個環境裡也內置了一些用來操作以太坊的 Javascript 對象，也可以在 GO-Ethereum 的官方文件找到一些指令！
```
> eth.accounts
[]
> personal.newAccoubt("PasswordHere")
...
> exitJA
```


|對象|功能|
| -------- | -------- |
|eth|操作區塊鏈|
|net|查看 P2P 網路狀態|
|admin|管理節點|
|miner|啟動 & 停止挖礦|
|personal|管理帳戶|
|txpool|查看交易內存池|
|web3|包含以上對象，以及單位換算的方法|

在Private Network中進行挖礦
```
> eth.coinbase;
["0x....."]
> miner;
...
> miner.setEtherbase(eth.accounts[0]);
true
> miner.start(1);
null (on the another powershell, we will see the new blockchain is mining)
> web3.eth.getBalance(eth.accounts[0]);
...
>
```
    
GethTips
```
$ geth --datadir .\privateChaindata\ --nodiscover --unlock 0 --mine 0
```
* --unlock 0 means unlock first account
* --mine 0 means mining with one fret

|參數|介紹|
| -------- | -------- |
|networkid|同見上文的 ChainID 部分以及 EIP-155。這邊要注意的是 networkid 與創世區塊的 chainID 必須相等，否則交易時會出現錯誤。|
|mine|啟動挖礦功能|
|rpc|啟用 HTTP-RPC 通訊協定功能，如此錢包應用程式就可以藉由 http 來連動這個挖礦節點。如果需要佈署智慧合約就需要將其加入。|
|rpcaddr|指定 HTTP-RPC 的 listening interface，預設為 “localhost”|
|rpcport|指定 HTTP-RPC 的 listening port（網絡監聽連接埠），預設為 8545|
|rpcapi|指定透過 HTTP-RPC interface 可以適用的 API，預設為 “eth,net,web3”|
|rpccorsdomain|允許跨網域的存取調用，\*代表來自任何網段。 當使用瀏覽器的錢包或 Solidity 編輯器（Remix）來部署智能合約時，就會需要注意這個部分|
|nodiscover|不搜尋其他網段上的節點。會停止端點搜尋機制，也就是說沒有其他 local 網路裏的節點可以發現我們的節點。|
|console|這是一個交互式的 Javascript 執行環境，可以在裡面執行 Javascript 的相關程式碼和可以在Geth中執行命令|
