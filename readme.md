Go-Ethereum，又簡稱Geth，為：
Ethereum Protocal
Command Line 工具
當我們運行了Geth以後：
Geth會以區塊鏈節點的方式運行
Geth會找到其他區塊鏈節點並與其連接
Geth會在本地端打開一JavaScript RPC，讓我們可以以此和節點互動
開始連接節點，下載（複製）區塊鏈上的資料
區塊鏈（資料）會存在我們的本地端，並不存在任何的中央伺服器或者資料庫
Geth是如何同步於區塊鏈的：
Full SYNC
最為緩慢，同步過會永遠進行，理論上沒有停止的一天
複製內容包含Block Header + Block Bodies等，為全部的歷史交易資料
總資料量大小超過35GB
Fast SYNC(Default)
複製時間大約為幾個小時至一天，
複製內容包含所有區塊的Block Header + Block Bodies，但只同步最新的1024筆交易
Light SYNC(Experimental)
同步時間僅需幾分鐘，資料大小約幾百MB
複製內容只有Block Header，只同步最新的快取
安全性較低但速度非常快，屬於實驗性質的同步方式
Overview

不同的區塊鏈性質
公鏈（Public Blockchain）：任何人都可以平等地參與、挖礦、讀取鏈上資訊、進行交易，例如：以太坊主網和測試網。
聯盟鏈（Consortium Blockchain）：任何經過授權的節點都可以參與。
私有鏈（Private Blockchain）：只有鏈的擁有者可以參與、挖礦、讀取鏈上資訊、進行交易。
下載Geth
Windows 在這裡下載並選擇相對應的版本。 https://geth.ethereum.org/downloads/#
使用以下指令測試是否安裝成功。
geth -h
MacOSX 也可以直接使用：
brew tap ethereum/ethereum
brew install ethereum

geth --help
如果直接不帶任何參數的打開Geth：
$ geth
開啟一個P2P節點
下載所有的區塊資料到本地端電腦（每個區塊大約2MB）
同步模式預設為Fast Sync
若需要中斷區塊同步可使用ctrl+c
Geth 的Log-Output
INFO [02-19|00:18:12.450] Starting peer-to-peer node               instance=Geth/v1.10.14-stable-11a3a350/windows-amd64/go1.17.5
Geth並不具有自動更新的機制，所以我們需要時時刻刻的手動將其更新到最新版本
Ethereum Protocol和Geth有密不可分的關係
bits-version有32和64
INFO [02-19|00:18:12.417] Initialised chain configuration          config="{ChainID: 1 Homestead: 1150000 DAO: 1920000 DAOSupport: true EIP150: 2463000 EIP155: 2675000 EIP158: 2675000 Byzantium: 4370000 Constantinople: 7280000 Petersburg: 7280000 Istanbul: 9069000, Muir Glacier: 9200000, Berlin: 12244000, London: 12965000, Arrow Glacier: 13773000, MergeFork: <nil>, Engine: ethash}"
Configuration
chainID
INFO [02-19|00:18:12.472] IPC endpoint opened                      url=\\.\pipe\geth.ipc
Windows系統為pipe；Linux&Mac系統需要透過檔案傳送IPC
這裡並沒有HTTP RPC endpoint open(loclahost:8545)，所以我們無法在此直接連接瀏覽器
資料預設儲存位置
我們可以在此找到所有的區塊資料：User->AppData->Roaming->Ethereum->geth->chaindata
我們可以在此找到所有的密鑰資料：User->AppData->Roaming->Ethereum->geth->keystore
Linux: ~/.ethereum 
OSX: ~/Library/Ethereum 
Windows: ~/AppData/Roaming/Ethereum
Attach Geth
如果只輸入$ geth 作為命令，將會作為伺服器一樣的與外界連接。當我們先以 $ geth的方式啟動，再新開一個命令提示字元以以下方式啟動Geth，便可將新開的視窗做為客戶端：
$ geth attach
使用 JavaScript JSON RPC API
$ geth attach
// look for the module
$ admin
$ web3
$ eth
...
https://github.com/ethereum/go-ethereum/wiki/Management-APIs
Genesis.json Files
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


EIPs 是以太坊中的一套標準，內容會包含 core protocol specifications、用戶 API、和合約標準。
gas 是一個在與合約進行交易時的內部價格，當我們傳送一些指令給 Ethereum Virtual Machine (EVM) 來進行交易或與合約互動時就會消耗一定量的 gas。
Private Network中使用JSON API
打開一PowerShell作為伺服器端
$ geth --datadir .\privateChaindata\ --nodiscover
打開另一個PowerShell作為客戶端
$ geth attach ipc:\\.\pipe\geth.ipc
< 
Wellcome to the Geth Javascript console!
Attach Javascript Console
在這個交互式的 Javascript 執行環境中，> 是命令提示符。在這個環境裡也內置了一些用來操作以太坊的 Javascript 對象，也可以在 GO-Ethereum 的官方文件找到一些指令！
> eth.accounts
[]
> personal.newAccoubt("PasswordHere")
...
> exitJA

在Private Network中進行挖礦
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
GethTips
$ geth --datadir .\privateChaindata\ --nodiscover --unlock 0 --mine 0
--unlock 0 means unlock first account
--mine 0 means mining with one fret
