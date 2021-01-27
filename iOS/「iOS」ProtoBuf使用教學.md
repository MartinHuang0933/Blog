# ProtoBuf

Date: Jan 27, 2021
Tags: Dev, iOS

# 什麼是 Protobuf

# 使用 Protobuf 有什麼好處？

# 安裝

本次我們會透過 Homebrew 來安裝 Protobuf ，所以在安裝前我們可以先檢測一下是否已安裝過 Homebrew。

```bash
# 檢測有無安裝 homebrew
brew -v
# 如果沒有安裝，可以透過以下指令安裝
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

接著安裝 Protobuf Compiler ，整套會需要安裝 automake、libtool、protobuf 。

```bash
brew install automake 
brew install libtool 
brew install protobuf
```

接下來我們要把 Github 上的 protobuf 下載回來，並且執行位於 `protobuf/objectivec/DevTool` 下的  `full_mac_build.sh` 執行檔

```bash
git clone https://github.com/google/protobuf.git
cd protobuf/objectivec/DevTools
./full_mac_build.sh
```

接著要在 Xcode 專案中加入 protobuf library，可以使用 Cocoapods 或是直接把檔案下載拉進專案中，但建議使用 Cocoapods 會比較方便。

```bash
# podfile
pod 'Protobuf'
```

# 建立 proto 檔與編譯

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

我們準備好 proto 檔以後，第二步要開始進行編譯