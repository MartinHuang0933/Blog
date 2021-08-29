---
tags: iOS
---

# iOS | Unable to simultaneously satisfy constraints 約束設定錯誤處理


在iOS開發中，幾乎所有頁面都會使用Autolayout的方式去排版，而在設定完LayoutConstraints後，有時會在Runtime出現一些Error log，雖然不一定會影響顯示出來的排版，但有報錯基本上就要修正。



## Error Log
```Objective-C
[LayoutConstraints] Unable to simultaneously satisfy constraints.
	Probably at least one of the constraints in the following list is one you don't want. 
	Try this: 
		(1) look at each constraint and try to figure out which you don't expect; 
		(2) find the code that added the unwanted constraint or constraints and fix it. 
(
    "<MASLayoutConstraint:0x600000393660 UIButton:0x7fe97b466f20.right == UIView:0x7fe97b458ba0.right - 20>",
    "<NSLayoutConstraint:0x6000004f1a90 UIImageView:0x7fe97b4671d0.leading == UIButton:0x7fe97b466f20.trailing + 8>",
    "<NSLayoutConstraint:0x6000004f1540 UIView:0x7fe97b458ba0.trailing == UIImageView:0x7fe97b4671d0.trailing + 20>"
)

Will attempt to recover by breaking constraint 
<NSLayoutConstraint:0x600001baebc0 UIImageView:0x7fe97b4671d0.leading == UIButton:0x7fe97b466f20.trailing + 8>

Make a symbolic breakpoint at UIViewAlertForUnsatisfiableConstraints to catch this in the debugger.
The methods in the UIConstraintBasedLayoutDebugging category on UIView listed in <UIKitCore/UIView.h> may also be helpful.
```

## 什麼情況會出現這個錯誤？

一般來說，出現這種警告有兩種情況：  
1. 使用xib的內建View，對其中子元件增加了Autolayout約束，在使用xib後又調整了View的框架
2. 純Code的方式使用Autolayout，但沒有對使用的`ViewAutoresizingMaskIntoConstraints`的屬性設置為false。

## 如何快速找出有問題的元件？

其實在Error log中有給出很明錯錯誤的地方，那就是在log中`Will attempt to recover by breaking constraint`的下一行，就是我們要尋找的約束。  

```Objective-C
// 關鍵線索
Will attempt to recover by breaking constraint 
<NSLayoutConstraint:0x600001baebc0 UIImageView:0x7fe97b4671d0.leading == UIButton:0x7fe97b466f20.trailing + 8>
```

但初學者在看到這個錯誤時會特別的頭痛，因為在Error log中通常只會寫出哪個元件的類型，例如：NSLayoutConstraint、UIImageView等等，這樣對於比較複雜的頁面會很難找出錯誤的約束。

Xcode其實提供了一個很方便的Debug功能，可以幫助我們迅速地找到錯誤的約束，而我們要使用的就是Xcode內建的[**Debug View Hierarchy**](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/Xcode_Overview/ExaminingtheViewHierarchy.html)。

## 使用Debug View Hierarchy找出元件

### **STEP 1：** 執行App

### **STEP 2：** 點選 Xcode 下方的 Debug View Hierarchy

<img src="https://i.imgur.com/5er5pnq.png" width="457" height="450">
  
  這時候你會看到整個App所有View的結構，是不是很方便呢
  
  ![](https://i.imgur.com/PYieUlW.gif)

  
### **STEP 3：** 找出錯誤約束的實體位址

還記得剛剛我們有特別找出錯誤的約束嗎？
```Objective-C
Will attempt to recover by breaking constraint 
// 我們要的就是NSLayoutConstraint的實體位址0x600001baebc0
<NSLayoutConstraint:0x600001baebc0 
UIImageView:0x7fe97b4671d0.leading == UIButton:0x7fe97b466f20.trailing + 8>
```
而我們要找的就是第3行的部分，將NSLayoutConstraint的實體位址`0x600001baebc0`複製起來。

### **STEP 4：** 用實體位址去找出錯誤的約束在哪裡

接著我們去左邊Debug navigator搜尋，並以剛剛的`0x600001baebc0`過濾元件，這時候就可以很快速的找到出錯的約束了。

<img src="https://i.imgur.com/W9FnCht.png" width="465" height="380">


## 總結

以前在初學的時候，遇到這個問題總是很頭痛，有一種大海撈針的感覺。  
而現在透過Xcdoe內建的Debug View Hierarchy就可以很快速的找到特定的元件，有時也可以查看UI元件的排版是不是有誤，真的是很方便很多。








