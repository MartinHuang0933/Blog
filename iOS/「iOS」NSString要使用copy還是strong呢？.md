---
tags: iOS
---

# iOS | NSString 要使用 copy 還是 strong 呢？

最近在複習 iOS 面試題系列，看到這一題想起幾年前剛接觸 Objective-C ，剛開始觀念還沒有建立起來，也曾經因為這個屬性花了不少時間Debug，雖然現在觀念已經搞懂了，但還是趁這個機會複習筆記一下。 

## 什麼情況下會有差異？

你可以先記得，大部分的情況都會使用copy。

而 copy 與 strong 的差別，在於 copy 是儲存「值」，strong 是儲存「記憶體位址」。

## 舉個例子

```objectivec=
// .h file
@property (strong,nonatomic) NSString *strongStr;
@property (copy, nonatomic)  NSString *copyStr;
```
```objectivec=
// .m file
NSMutableString *mutableStr = [NSMutableString stringWithFormat:@"abc"];

// 將mutableStr賦值給retainStr、copyStr
self.strongStr = mutableStr;
self.copyStr = mutableStr;

// 將 mutableStr 更改為 xyz
[mutableStr appendString:@"xyz"];

// 印出三個字串的值
NSLog(@"mutableStr = %@",mutableStr);
NSLog(@"strongStr = %@",self.strongStr);
NSLog(@"copyStr = %@",self.copyStr);

// 輸出值
// mutableStr = xyz
// strongStr = xyz
// copyStr = abc

```

可以發現，此時 copy 屬性字串並不會因為賦值來源而更改值，反而是strong屬性的字串隨著來源而改變了。   
這是因為 copy 屬性是對於來源做了深拷貝，是將 mutableStr 的數值複製到了 copyStr 上；而 strong 屬性是指向來源的記憶體位址，所以當原本的 mutableStr 改變時，strongStr 也會跟著改變。

---

接著我們在來看一下這三個字串的記憶體位址
```objectivec=
// 印出三個字串的記憶體位址
mutableStr = abcxyz
StrStrong = abcxyz
StrCopy = abc

// 輸出記憶體位址
// mutableStr = 0x6000030ee520
// StrStrong = 0x6000030ee520
// StrCopy = 0xc09e09c05a166394
```

這時候我們可以看到 mutableStr 與 StrStrong 的記憶體位址是相同的，代表他們兩個儲存值的地方是一樣的，所以才會造成改變 mutableStr 後 StrStrong 也跟著更改的情況。

## 小結

比較簡單的記憶法就是 NSString 用 Copy 就好，因為實務上也很少會使用    Strong 屬性的情境。  
雖然現在 Objective-C 比較少人在用了，不過有時候面試還是會考到類似的觀念題，所以邊複習邊整理文章，希望對開發者有些幫助。
