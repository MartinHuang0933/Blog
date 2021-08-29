---
tags: iOS
---

# 「iOS」Singleton Design Pattern 單例設計模式


## 什麼是Singleton？

在許多物件導向設計的程式語言中，單例設計模式非常常見，而他的實際效益就是可以確保在整個應用程式中只會有一個實體，且生命週期也跟應用程式相同，在應用程式啟動時就會自動幫我們建立了。
在iOS開發學習的過程中也許你已經用了許多Singleton的物件了，只是你還不知道而已，例如：UIColor、FileManager等等。 

## Singleton 會用在哪些場合？

Singleton因為具有唯一性，所以通常會用於：
1. 環境變數、已定義的參數檔案
2. 常用的資料處理算法，如字串轉換、時間轉換、資料庫連接、網路連接等等。

## Singleton 怎麼寫？

建立Singleton最基礎的寫法就是判斷實體是不是為空值，如果是空值就會直接建立。
在建立時需要注意執行緒衝突，所以我們需要多加上執行緒的安全機制，實際程式碼如下。

```Objective-C
// Objective-C 版本
+ (instancetype)shareManager{
    @synchronized(self){
        if (!manager) {
            manager = [[self alloc]init];
        }
        return manager;
    }
}
```
也可以透過GCD的方式建立
```Objective-C
// Objective-C 版本 , 使用GCD
static DataManager *manager = nil;

@implementation DataManager

+ (instancetype)shareManager{
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        manager = [[self alloc] init];
    });
    return manager;
}
@end
```

此時Singleton就已經寫好了，只要我們在外面呼叫shareManager這個方法，返回的實體始終是一個，因為dispatch_once只執行一次。

```Objective-C
// Objective-C 版本 , 呼叫 Singleton
{
    [DataManager shareManager];
}
```





