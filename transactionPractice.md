
Overview

### 1 Install Geth
Windows 在這裡下載並選擇相對應的版本。 https://geth.ethereum.org/downloads/#
使用以下指令測試是否安裝成功。
geth -h
MacOSX 也可以直接使用：
brew tap ethereum/ethereum
brew install ethereum

geth --help


### 2 Create Directory Structure
隨意建立一個資料夾，假設叫做「myPrivateChain」，進入該資料夾當作我們目前的專案位置。

### 3 Create a Genesis Configuration
建立創世區塊的參數用檔案，將其命名為 genesis.json。
{
    "config": {
        "chainID": 123456,
        "homesteadBlock": 0,
        "eip150Block": 0,
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
    "gasLimit": "0x2fefd8",
    "alloc": {
        "0x0000000000000000000000000000000000000001": {
            "balance": "11111111111"
        },
        "0x0000000000000000000000000000000000000002": {
            "balance": "22222222222"
        }
    }
}

EIPs 是以太坊中的一套標準，內容會包含 core protocol specifications、用戶 API、和合約標準。
gas 是一個在與合約進行交易時的內部價格，當我們傳送一些指令給 Ethereum Virtual Machine (EVM) 來進行交易或與合約互動時就會消耗一定量的 gas。

### 4 Initialize the genesis.json
初始化，輸入以下指令來建立初始區塊。
$ geth --datadir <path-to-data-directory> init <path-to-genesis-block>
--datadir data : 使區塊儲存於 data 資料夾中。

### 5 Start!
啟動私有鏈
geth --datadir data --networkid 123456 console
networkid 選項後面跟一個數字，指定這個私有鏈的網絡 ID 為 123456。

更多參數（查看 go 之後有些已不能使用）
$ geth --datadir <path-to-data-directory> --networkid 123456  --nodiscover console

如果要查看更多的參數可以參閱 Management-APIs

### 6 Attach Javascript Console
在這個交互式的 Javascript 執行環境中，> 是命令提示符。在這個環境裡也內置了一些用來操作以太坊的 Javascript 對象，可以直接使用這些對象：

也可以在 GO-Ethereum 的官方文件找到一些指令！


### 7 Management the Private Chain
查看所有帳號還有帳號存款，accounts 是一個陣列所以也可以使用[] 陣列取值運算子來取用，也可以使用新變數來表示帳號字串:
> eth.accounts
> eth.getBalance("0x748Cf24cc44Cc20239662eb04dF48ce7Aa019E16")
> myEthereumAddress = eth.accounts[0]
查看節點資訊（如果要使用更多 admin 指令可以參閱在 go-ethereum 的 Wiki）：
> admin.nodeInfo
建立新帳戶，其中的字串為預設密碼：
> personal.newAccount("88888888")
查看礦工帳號，系統預設礦工帳號會是第一個帳號：
> eth.coinbase

### 8 Transfer Ether
解鎖帳號。每次進行交易都需要解鎖帳號，這邊的 myEthereumAddress 要填入想要交易的帳號，也可以使用 eth.accounts[1] 取用所有帳號裡面的任一個：
personal.unlockAccount(eth.accounts[1])
之後出現的 Passphrase 就是輸入密碼的意思，輸入創建晃時的密碼就可以成功解鎖！
發送交易，我們想要將 eth.accounts[1]的錢錢匯給 myEthereumAddress：
> amount = web3.toWei(10,'ether')
"10000000000000000000"
> eth.sendTransaction({from:eth.accounts[1],to:myEthereumAddress,value:amount})
這裡需要注意的是如果匯出帳戶沒錢也會出現錯誤！
此時來查看 myEthereumAddress 的餘額：
> eth.getBalance(myEthereumAddress)
0
發現沒有被處理，此時我們可以查看 txpool 來驗證，發現其中有一條 pending 的交易，pending 就表示已提交但還未被處理的交易。
> txpool.status
{
  pending: 1,
  queued: 0
}
要使交易被處理就需要挖礦啦，詳情請看下個章節！

### 9 Mining Time
我們可以使用以下指令來更改礦工帳號，這樣挖礦時錢就會轉到對應的帳號。
> miner.setEtherbase(eth.accounts[1])
我們可以執行以下步驟，首先語法 miner.start(1) 會開始挖礦，最後 miner.stop() 就是停止挖礦。
> miner.start(1)
> miner.stop()
其中 miner.start(1) 的參數為 CPU 使用數。
挖礦結束後再回來查看一次 txpool，發現 pending 的交易數量為 0。
> txpool.status
{
  pending: 0,
  queued: 0
}
再查看一次接受轉帳的帳戶是否有收到匯款了！
> web3.fromWei(eth.getBalance(myEthereumAddress),'ether')
同時我們也可以使用以下指令來查看區塊數量。
eth.blockNumber

---

## HW Reference:
http://netkiller.sourceforge.net/blockchain/ethereum/genesis.json.html
https://dotblogs.com.tw/explooosion/2018/07/29/172031
https://medium.facilelogin.com/build-your-own-blockchain-b8eaeea2f891

https://www.books.com.tw/products/0010798325
https://learnblockchain.cn/2018/03/18/create_private_blockchain/
https://medium.com/samumu-clan/%E7%94%A8-geth-%E6%9E%B6%E8%A8%AD%E7%A7%81%E6%9C%89%E9%8F%88-41a2baa0efd8
https://medium.com/swlh/how-to-set-up-a-private-ethereum-blockchain-c0e74260492c
https://www.cnblogs.com/soowin/p/14328195.html

https://ithelp.ithome.com.tw/2020ironman/articles/2255?page=1
https://medium.com/swlh/how-to-set-up-a-private-ethereum-blockchain-c0e74260492c
https://www.section.io/engineering-education/how-to-build-your-own-private-blockchain/
https://medium.com/samumu-clan/%E7%94%A8-geth-%E6%9E%B6%E8%A8%AD%E7%A7%81%E6%9C%89%E9%8F%88-41a2baa0efd8
https://medium.facilelogin.com/build-your-own-blockchain-b8eaeea2f891
https://souptacular.gitbooks.io/ethereum-tutorials-and-tips-by-hudson/content/private-chain.html
https://www.antiersolutions.com/

Windows
https://medium.com/%E4%B8%80%E5%80%8B%E5%AE%B9%E6%98%93%E5%81%A5%E5%BF%98%E7%9A%84%E5%A4%A7%E5%AD%B8%E7%94%9F/5%E5%88%86%E9%90%98%E5%BB%BA%E7%AB%8B%E4%BB%A5%E5%A4%AA%E5%9D%8A%E7%A7%81%E6%9C%89%E9%8F%88-%E4%BB%A5poa%E5%85%B1%E8%AD%98%E7%82%BA%E4%BE%8B-part-1-c6e9daad71a7
https://merehead.com/blog/how-to-create-private-ethereum-blockchain/
https://programmer.help/blogs/build-ethereum-private-chain-windows.html
Mac OS X
https://learnblockchain.cn/2018/03/18/create_private_blockchain/
https://notes.andywu.tw/2018/%E5%BB%BA%E7%AB%8B%E4%BB%A5%E5%A4%AA%E5%9D%8A%E7%A7%81%E6%9C%89%E9%8F%88%E4%B8%80/
https://programmerall.com/article/3909456580/
https://github.com/chafey/ethereum-private-network
UNIX - GNU/Linux, Ubuntu
https://medium.com/@pradeep_thomas/how-to-setup-your-own-private-ethereum-network-f80bc6aea088
https://ithelp.ithome.com.tw/articles/10203475
https://arctouch.com/blog/how-to-set-up-ethereum-blockchain/
