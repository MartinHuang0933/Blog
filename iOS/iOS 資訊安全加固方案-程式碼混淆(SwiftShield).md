---
tags: iOS, 資訊安全, 混淆
---

# iOS 資訊安全加固方案 - 程式碼混淆(SwiftShield)
tttest
![](https://i.imgur.com/Xa73TNl.png)

## 為什麼要做程式碼混淆？

最近專案接到需求，為了避免程式被反解譯，也避免程式的核心業務邏輯暴露在外，故開始尋找 App 加固的方案。  
資訊安全這門領域其實很深，那既然剛入門我們就從基礎的開始，程式碼混淆在資安方案中算是 CP 值比較高的，開發者混淆時付出一些時間(熟悉以後還可以自動化)，但讓駭客就要多付出好幾倍的時間去破解你的程式，所以在基礎的資安加固方案中會比較推薦程式碼混淆。  
 .  
## 如何做到程式碼混淆？

市面上有許多的混淆工具，有收費的也有開源的，如果只是想試點水溫會建議用開源的工具玩玩看。但如果專案有時程壓力，可以試試付費的工具，畢竟省時省力 😂。

這邊我列舉出幾個比較常見的開源混淆工具：

#### [(1) 「rockbruno/SwiftShield」](https://github.com/rockbruno/swiftshield):
* 這是我比較推薦的一個，效能不錯、評價高、重點是還有在維護。
* 手動模式（Objective-C、Swift）：採後綴混淆，簡單易用，缺點是有特定後綴的才會混淆，不適合已開發的專案。
* 自動模式（Swift）：全局混淆，也可以指定特定資料夾不混淆，缺點是有些語法混淆後會出錯，需要手動修復或是修改原本的程式寫法，但錯誤通常不多。

#### [(2) 「LennonChin/Code-Confuse-Plugin」](https://github.com/LennonChin/Code-Confuse-Plugin/blob/master/README_zh-cn.md):
* 僅混淆 Objective-C 項目(.h.m)，Swift 需指定忽略。
* 需求為 Python3，以 Define 定義來替換方法名、類別名。

#### [(3) 「onorZhang/iOSConfuse」](https://github.com/rockbruno/swiftshield):
* 採用前綴的方式進行混淆，一樣不適合已開發的專案。

## 使用 SwiftShield 進行程式碼混淆

SwiftShield 是一個將 iOS 專案程式碼產製亂數加密名稱的工具，其混淆範圍包含程式碼、Pod 和 Storyboard，可以保護應用程序避免被輕易的反解譯（如 class-dump 和 Cycript）。

### 混淆後會是什麼樣子？
```swift=
class fjiovh4894bvic: XbuinvcxoDHFh3fjid {
  func cxncjnx8fh83FDJSDd() {
    return vPAOSNdcbif372hFKF()
  }
}
```
### 自動模式（限 Swift 使用）
使用`-automatic`指令，SwiftShield 將使用 SourceKit 自動混淆整個專案，混淆範圍包含程式碼、Pod 和 Storyboard。

#### 注意事項 ⚠️
1. 目前有一些 SourceKit Bugs 及不會混淆的類型需注意，如果專案中剛好有遇到特定情境可能需要修改程式碼，詳請請參照[本連結](https://github.com/rockbruno/swiftshield/blob/master/SOURCEKITISSUES.md)。
2. 檔案名稱及資料夾名稱不可包含空白，否則執行混淆時會報錯。
3. 檔案名稱及資料夾名稱若有"-"依然會被混淆。

#### 執行步驟
1. 從[此處](https://github.com/rockbruno/swiftshield/releases)下載最新的 SwiftShield
2. 打開終端機，`cd` 移動至下載的 Swiftsheild 文件夾下
3. 設置 SwiftShield 的使用權限
```bash=
# 給予讀、寫、執行的權限
$ sudo chmod -R 777 ./swiftsheild(文件路徑)

# 如果使用CocoaPods，需要解鎖權限
$ sudo chmod -R 774 [專案路徑]
```
4. [官方文件](https://github.com/rockbruno/swiftshield/blob/master/USAGE.md)有提到，如果有使用到會修改文件的第三方框架，如：`R.Swift`、`SwiftGen`，需在專案中的 `TARGETS -> Build Phases -> Run Script` 修改指令，這裡以 `R.Swift` 為範例：

![](https://i.imgur.com/2jheJ7C.png =500x)

```bash=
# 原本的 Run Script
"$PODS_ROOT/R.swift/rswift" generate "$SRCROOT/[專案名稱]"

# 修改後的 Run Script
if [ "$SWIFTSHIELDED" != "true" ]; then
    "$PODS_ROOT/R.swift/rswift" generate "$SRCROOT/[專案名稱]"
fi
```
6. 準備就緒後就可以來下指令進行混淆了，自動模式的指令有以下幾種

    **必填參數：**
    * `-automatic`：啟用自動模式。
    * `-project-root`：專案項目的根目錄。 SwiftShield 會向下搜索你的項目文件。
    * `-automatic-project-file`：專案項目的主要`.xcodeproj` / `.xcworkspace`文件。
    * `-automatic-project-scheme [myScheme]`：專案項目的Scheme。

    **可選參數**：
    * `-sdk`：防止混淆公共類別和方法。如果你正在構建SDK項目，請使用它。
    * `-ignore-modules`：防止混淆某些資料夾，並以逗號分隔。如果無法正確混淆某個資料夾，請使用此選項。 要注意的是，是輸入資料夾的確切名稱，而不是目標名稱。例：`MyLib`,`MyAppRichNotifications`,`MyAppWatch_Extension`
    * `-show-sourcekit-queries`：打印發送到 SourceKit 的查詢。要注意的是這指令會造成非常龐大的輸出，因此只能將其用於錯誤報告和功能開發！
    
    **實際組出來以後**
```bash=
$ #指令結構
$[SwiftShield 路徑]+空白+"swiftshield"+空白+"-automatic"+空白+"-project-root"+空白+"-automatic-project-file"+空白+[xcworkspace 路徑]+空白+"-automatic-project-scheme"+空白+[專案Target]+空白+"-ignore-modules"+空白+[要忽略的項目(含Pods項目，且需以資料夾名稱為主)]

$ #實際執行指令
$ /Users/mac/swiftshield-3.5.1/bin/swiftshield swiftshield -automatic -project-root /Users/mac/[專案資料夾名稱] -automatic-project-file /Users/mac/[專案名稱].xcworkspace -automatic-project-scheme [專案Target] -ignore-modules Alamofire,MJRefresh,SDWebImage...
```
6. 指令需要執行一小段時間，過程會看到`Building project to gather modules and compiler arguments...`、`Overwriting`、`Checking`等訊息。

     ![](https://i.imgur.com/aAniZCn.png)
7. 接著直接打開專案執行看看，通常會出現一點混淆錯誤的Error，基本上都是重複的錯誤，這部分跟程式寫法有關。若不想改動原本程式碼，基本上混淆後再逐一修改即可發布。

## 混淆失敗怎麼辦？
**編譯過程失敗**
1. 檢查所有檔案名稱或資料夾名稱是否有包含空白。
2. 檢查是否給與正確的權限。
3. 打開下載的 swiftshield 執行檔是否有被 MacOS 安全性阻擋。

**混淆後專案執行失敗或 Crash**
1. 檢查是否有使用到[SourceKit Bugs 及不會混淆的類型](https://github.com/rockbruno/swiftshield/blob/master/SOURCEKITISSUES.md)。
2. 帶 selector 方法的參數中若含有字串，盡量用變量或常量代替
```swift=
// 修改前
let item1 = UIBarButtonItem(title: "取消", style: .plain, target: self, action: #selector(cancelButtonClick(sender:)))
//混淆後會​​報錯，混淆後會把）}一起混淆進去，導致項目缺少）}而報錯

// 修改後
let cencelTitle = "取消"
let item1 = UIBarButtonItem(title: cencelTitle, style: .plain, target: self, action: #selector(cancelButtonClick(sender:)))
```
3. `.xib` 文件有可能不會連動到混淆後的類別，要修改`File's Owner`
4. 若錯誤不多可以手動修復，混淆後會產生 swiftshield-output 資料夾，內有conversionMap.txt，可以對應混淆前後的字串。

## 對加密的 Crash logs 進行反混淆處理

成功混淆專案後，SwiftShield 將產生一個輸出文件夾，其中包含一個conversionMap.txt 文件，其中包含對該專案所做的所有更改，讓你查明加密對象的前後的字串。
```txt=
//
//  SwiftShield
//  Conversion Map
//  Automatic mode for MyApp 2.0 153, 2018-09-24 10.23.48
//

Data:

ViewController ===> YytSIcFnBAqTAyR
AppDelegate ===> uJXJkhVbwdQGNhh
SuperImportantClassThatShouldBeHidden ===> GDqKGsHjJsWQzdq
```
如果你要對混淆後的程式碼文件進行復原，可以透過以下指令復原
```bash=
$ swiftshield -deobfuscate CRASH_FILE -deobfuscate-map PATH_TO_CONVERSION_MAP
```

## 總結
資訊安全的領域很深，從連線加密、程式碼混淆、LLVM編譯器混淆等等一關接著一關，都是為了保護重要的機密或接口不會被竊取盜用，造成商業損失。  
其實 iOS 系統已經是相對安全的，但對於通訊、金融行業還是要有一定程度的資安保護，畢竟任何資安加固的措施都不能保證百分之百安全，但透過增加駭客反編譯的難度，就能有效降低駭客破解的速度及讓駭客放棄破解打退堂鼓。



## Reference
1. [SwiftShield](https://github.com/rockbruno/swiftshield)
2. [SwiftShield代碼混淆](https://www.jianshu.com/p/4b624e59ad5c)
3. [Swiftshield：SwiftOBJ-C 代碼混淆工具](https://www.freebuf.com/sectool/213922.html)

另外我在搜尋資料時也意外發現有幾篇文章不錯，也分享跟大家。
1. [資訊安全從連線加密入手](https://medium.com/@SunXiaoShan/%E8%B3%87%E8%A8%8A%E5%AE%89%E5%85%A8%E5%BE%9E%E9%80%A3%E7%B7%9A%E5%8A%A0%E5%AF%86%E5%85%A5%E6%89%8B-58f727c1ef6c)



