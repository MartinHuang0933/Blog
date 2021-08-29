---
tags: 其他
---

# 「Mac」解決 Touch Bar 失效問題

自從蘋果在 2016 年在 Macbook 新增 TouchBar 之後，Macbook的使用上又多了一種方式。
但有時候在使用上會遇到一些奇怪的Bug，舉凡 TouchBar 失靈、Esc消失等等，在這裡我記錄了一些解決的方式。

## 最快解決方案 - 指令

基本上最快的方式就是去終端機下指令，直接由指令重啟 TouchBar 。
```
> killall ControlStrip
```

---

但後來我覺得要記得指令實在是有點違反人性，因為不是每天都會發生失靈的狀況。  
所以我把指令包成 command file，只要連點兩下就可以執行了。

[下載連結](https://drive.google.com/uc?export=download&id=18A7xfv7LN5xIWIk_f0_17bnpGOkxuwY6)

有需要的可以下載使用。


