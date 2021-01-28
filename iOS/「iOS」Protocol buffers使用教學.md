
---

title: 「iOS」Protocol buffers使用教學
date: 2021-01-28 11:55:27
tags:

- iOS
- Dev

categories: 程式開發
thumbnail: https://raw.githubusercontent.com/MartinHuang0933/martinios-source/master/Article/iOS-ProtocolBuffers/banner.png
cover: https://raw.githubusercontent.com/MartinHuang0933/martinios-source/master/Article/iOS-ProtocolBuffers/banner.png

---

[Protocol Buffer](https://developers.google.com/protocol-buffers) 是 2008 年由 Google 開發的一種跨平台、跨語言的數據交換格式，是一種將結構化資料序列化（變成二進位制）的方法，Google 提供了多種程式語言的實作方式，每一種實作都包含了相對應語言的編譯器以及 Library。

你可以把它想成另一種形式的 JSON ，但資料要比 JSON **格式更小、傳輸更快**，所以用於資料傳輸、資料儲存上面會比 XML、JSON 有更大的優勢。

<!-- more -->

　

# Protocol Buffer 的優缺點

### 優點

- 檔案小、傳輸速度快
- 適用於多種語言，透過工具可以快速的建立好 Model
- 前後端使用同一個 proto 檔進行編譯，減少溝通成本

### 缺點

- 編譯後的檔案可讀性差
- 安裝步驟較為繁瑣

　

# Protocol Buffer 的格式

Protocol Buffer 的格式大致上長這樣，目前有 [proto2](https://developers.google.com/protocol-buffers/docs/reference/proto2-spec) 、[proto3](https://developers.google.com/protocol-buffers/docs/proto3) 兩種版本，但目前以 proto3 居多，也較嚴謹：

```
syntax = "proto3";
message Person {
  int32 id = 1;
  string name = 2;
  string email = 3;
}
```

上方的 `id = 1` ,  `name = 2` 並不是賦值，而是編號、Key的意思，最後編譯時 Protocol Buffer 才可以依照編號去做比對，並且編號在同一個 Model 內不可以重複，基本上都會從 `1` 開始依序往下排。
另外建議欄位編號介於 `1~15` 之間，因為在這個範圍會用 `1 byte` 做編譯，而 `16~2047` 會用 `2 bytes` 做編譯，詳情可以看[這裡](https://developers.google.com/protocol-buffers/docs/proto#simple)。

 

　

# 安裝

本次我們會透過 **Homebrew** 來安裝 Protobuf ，所以在安裝前我們可以先檢測一下是否已安裝過 Homebrew。

```bash
# 檢測有無安裝 homebrew
$ brew -v
# 如果沒有安裝，可以透過以下指令安裝
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

  

接著安裝 Protobuf Compiler ，整套會需要安裝 automake、libtool、protobuf 。

```bash
$ brew install automake 
$ brew install libtool 
$ brew install protobuf
```

  

接下來我們要把 Github 上的 protobuf 下載回來，並且執行位於 `protobuf/objectivec/DevTool` 下的  `full_mac_build.sh` 執行檔

```bash
$ git clone https://github.com/google/protobuf.git
$ cd protobuf/objectivec/DevTools
$ ./full_mac_build.sh
```

  

　

# 建立 proto 檔與編譯

### **STEP 1**

第一步我們先建立 proto 檔，副檔名為 .proto，內容的格式如下。

```
syntax = "proto3";
package package_name;

message TradingAccountRequest {
  string sessionId = 1;
  string userToken = 2;
  string language = 3;
}
```

這裡要注意的是 proto 使用的版本，proto2 與 proto3 有滿大的不同，例如 proto3 就移除了 optional 這個關鍵字，並預設所有欄位都是 optional ，如果搞錯了就會出現以下錯誤。

```
Explicit 'optional' labels are disallowed in the Proto3 syntax. To define 'optional' fields in Proto3, simply remove the 'optional' label, as fields are 'optional' by default.
```

  　

### **STEP 2**

我們準備好 proto 檔以後，第二步要開始進行編譯。

開啟 src 資料夾建立 protocols 與 gen 資料夾，把 proto 檔放到 protocols 資料夾中。

並開啟 Terminal cd 到 `src/` 下使用 `protoc` ，輸入 proto 檔來源與輸出位置後進行編譯。

```bash
$ cd src
$ protoc --proto_path=protocols --objc_out=gen protocols/*.proto
```

其中的 `--proto_path` 是 proto 檔的來源資料夾位址，就是剛剛建立的 protocols 資料夾。

而 `--objc_out` 就是編譯後的 `pbobjc.h` `pbobjc.m` 檔案輸出位址，產製好`pbobjc.h` `pbobjc.m` 以後，我們將這兩個檔新增至專案中。

  　

### **STEP 3**

最後一步，因為 Protobuf 使用的是 MRC，而現在的專案基本上都是使用 ARC 了，所以我們要去 `Build Phase -> Compile Sources`，找到我們剛剛新增的`pbobjc.m` ，並加上 `fno-objc-arc` 這個Complier Flag。

  　

# 開始使用

經過了這麼多的步驟以後，我們終於可以開始在專案內使用了 🎊。

先在 Xcode 專案中加入 protobuf library，可以使用 Cocoapods 或是直接把檔案下載拉進專案中，但建議使用 Cocoapods 會比較方便。

```bash
# in podfile
pod 'Protobuf'
```

  

接著一般的 Model 物件一樣使用就可以了。

```objectivec
// 引用
#import "Tradingaccount.pbobjc.h"

// 使用
TradingAccountRequest *request = [TradingAccountRequest new];
request.language = @"";
request.sessionId = @"";
```

  　

# 總結

Protocol Buffers 帶給開發者另一種資料傳輸的方式，比起常用的 JSON 來說，在效能的優化上是滿明顯的。

但有優點必有缺點，Protocol Buffers 最大的缺點還是不便於直觀的閱讀，所以用起來沒那麼方便。

整體來說要依照開發的情況來去決定要使用哪一個，如果是大量的資訊傳輸，那用 Protocol Buffers 會是一個不錯的選擇。

  　

### 參考資料

[Google Protocol buffers](https://developers.google.com/protocol-buffers)

[Protocol Buffer 入门使用: iOS](https://www.jianshu.com/p/b04e877898b9)

[protobuf3.1.0使用](https://www.jianshu.com/p/7aec4ef02473)

[Protobuf 语法指南](https://colobu.com/2015/01/07/Protobuf-language-guide/)
