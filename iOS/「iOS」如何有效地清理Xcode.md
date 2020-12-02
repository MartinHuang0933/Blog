---
tags: iOS , Xcode , IDE
---

# 如何有效地清理 Xcode 

大家都知道 Xcode 除了是吃效能的怪獸以外，同時也是很會吃儲存空間的開發工具。  

若一開始再購買 mac 時沒有把硬碟空間買好買滿，那未來在開發時很有可能會遇到空間不足的問題。尤其是在 Xcode 改版時，如果要同時兼容新舊版本，那就會是一件非常痛苦的事。  

以下一些清空間的方法，希望可以幫助到還在苦海的各位 (我也曾經在那片海裡QQ)。


## 1. Build App caches 
**用途：** 儲存各App Build的結果，並生成索引  
**位置：** ~/Library/Developer/Xcode/DerivedData  
**刪除方法：** 直接刪除 `DerivedData` 內所有檔案即可  
**備註：** 對於 `DerivedData` 有興趣的可以看[這裡](https://www.cnblogs.com/zhanggui/p/11171642.html)

## 2. iOS DeviceSupport
**用途：** 儲存 iOS 的版本  
**位置：** ~/Library/Developer/Xcode/iOS DeviceSupport  
**刪除方法：** 直接刪除iOS版本即可，如`14.2 (18B92)`  
**備註：** 以實機開發時，每一台設備的版本被都會儲存起來，如果你用許多不同的iOS的版本在測試，那使用空間就會很可觀

## 3. Simulator devices
**用途：** 模擬器版本  
**位置：** ~/Library/Developer/CoreSimulator/Devices  
**刪除方法：** 至終端機下指令`xcrun simctl delete unavailable`  

## 4. Archives & .ipa file
**用途：** 每次打包專案時都會產生，用來提交 App Store 或是TestFlight 等等  
**位置：** ~/Library/Developer/Xcode/Archives  
**刪除方法：** 直接刪除即可  
**備註：** 若為已上線之產品，建議預留最後3-5個的版本

## 5. Logs
**用途：** 就，Log  
**位置：** ~/Library/Developer/Xcode/iOS Device Logs  
**刪除方法：** 直接刪除 `iOS Device Logs` 內所有檔案即可  

## 6. Others cache
**用途：** 整個資源庫的快取  
**位置：** ~/Library/Caches/  
**刪除方法：** 直接刪除 `Caches` 內所有檔案即可  
**備註：** 若不放心，可以將資料夾移到桌面，再重啟電腦看有沒有異狀。

---

## 工具推薦： 
[App DevCleaner for Xcode](https://apps.apple.com/us/app/devcleaner-for-xcode/id1388020431)  

![](https://i.imgur.com/54qD4vN.png){:height="700px" width="400px"}

![](https://i.imgur.com/54qD4vN.png =200x200)

![](https://gyazo.com/eb5c5741b6a9a16c692170a41a49c858.png | width=100)

非常實用的工具，可以快速清除 DeviceSupport、DerivedData、Archives 等等快取檔案。
詳細的使用方法可以參考[彼得潘的教學](https://medium.com/%E5%BD%BC%E5%BE%97%E6%BD%98%E7%9A%84-swift-ios-app-%E9%96%8B%E7%99%BC%E6%95%99%E5%AE%A4/%E5%88%AA%E9%99%A4xcode%E4%BD%94%E6%93%9A%E7%A1%AC%E7%A2%9F%E5%A4%A7%E9%87%8F%E7%A9%BA%E9%96%93%E7%9A%84derived-data-190c85eba79)

## 總結：

雖然這些肥大的檔案總是讓我們恨得牙癢癢，但不得不說這些檔案確實在編譯階段幫我們省下了不少時間，也是一種用空間換時間的概念。  

剛好遇到問題所以隨筆記錄一下，同時希望這些方法也有幫助到你。
