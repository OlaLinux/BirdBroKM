# Ch7 Linux 磁碟與檔案系統管理

2020/10/04

## 重點統整

### 磁碟分割方式

1. MBR
    * 第一個磁區最重要，包含
        * 主要開機表(MBR): 446 bytes
        * 分割表(partition table): 64 bytes
    * 預設只能切4筆data，此data用來記錄開始與結束的磁區
        * 主要分割槽(primary)
        * 延伸分割槽(extended)
            * 邏輯分割槽 (logical)(由延伸分割槽分割)
    * 例題：假如我的PC有兩顆SATA硬碟，我想在第二顆硬碟分割出6個可用的分割槽(可以被格式化來存取資料之用)， 那每個分割槽在Linux系統下的裝置檔名為何？且分割類型各為何？至少寫出兩種不同的分割方式。
    * P+P+P+E
![](https://i.imgur.com/LZt4pTN.png)
    * P+E
![](https://i.imgur.com/Y1vPiJ0.png)



    * MBR缺點: 
        * 作業系統無法抓取到 2.2T 以上的磁碟容量！
        * MBR 僅有一個區塊，若被破壞後，經常無法或很難救援。
        * MBR 內的存放開機管理程式的區塊僅 446bytes，無法容納較多的程式碼。


2. GPT
    * 採用LBA規劃磁區，為了與所有磁碟相容(MBR)，故每一LBA大小為512 bytes
    * LBA2-LBA33為實際分割表
        * 每一個LBA可記錄4筆data(分割資訊partition)，共可記錄32*4=128筆分割資訊
        * 單一分割槽最大可存放容量為2^64*512 bytes = 8ZB
            * GPT 在每筆紀錄中提供了 64bits 來記載開始/結束的磁區號碼
        * 整個磁碟最後33個LBA用來當分割資訊的備份 



        * ![](https://i.imgur.com/igpgkQu.png)

### EXT2檔案系統
![](https://i.imgur.com/UiEvwcA.png)

### 討論內容


#### Q1: 為什麼不是都放在一起？，那不就讀取時會比較快嗎

A: 因為檔案系統高達數百GB時， 那麼將所有的 inode 與 block 通通放置在一起導致數量太龐大，不容易管理。想像一下分群的話

* 都放在同一個block group，會有資安或是資料毀損沒辦法透過superblock取得資訊
* 或是夠多頭並行讀取

#### Q2: MBR磁碟分割表為何只能切4個partition?

A: 參考分割表結構，一張分割表需16 bytes，而MBR僅有64 bytes的空間可供分割表使用，故最多只能切4個partition
![](https://i.imgur.com/xMQOVLy.png)

#### Q3: 為何單一檔案系統最大上限為2TB?

A: 一樣參考分割表，其中分割區起始/結束區號的資訊佔了共4 bytes，故2^32^(總sector數) * 512 (bytes per sector) = 2^41^ = 2 TB (單一分割槽最大容量)


> 註:傳統的磁碟與檔案系統之應用中，一個分割槽就是只能夠被格式化成為一個檔案系統，所以我們可以說一個 filesystem 就是一個 partition


* 補充: [HDD硬碟如何讀取資料(動畫)](https://youtu.be/oEORcCQ62nQ?t=546)