# 「iOS」Lazy load 延遲載入

以往我們創建變數或物件時，通常會在建立完之後馬上進行初始化，以便後續的程式邏輯運用。  
那萬一初始化的順序沒有寫好時，很容易遇到使用到尚未初始化的變數，造成程式結果出錯，甚至要花時間去找是哪一個步驟出了問題。  

如果使用Lazy load創建物件或變數，就可以有效避免這個問題。

## 使用 Lazy load 有什麼好處 ?

Lazy load 可以讓你可以不必關心變數創建的時機，等到真正使用到變數時才去建立，同時也可以保證在使用時一定是被初始化過的狀態，提高記憶體使用效率。  

### Objective-C 寫法
```javascript=
// in @interface
@property (nonatomic, weak) UIButton *registerBtn;

// in @implementation
- (UIButton*)registerBtn
{
    if(!_registerBtn)
    {
        UIButton *button = [UIButton new];
        [button setTitle:@"註冊" forState:UIControlStateNormal];
        [button addTarget:self action:@selector(registerBtnClick) forControlEvents:UIControlEventTouchUpInside];
        
        // Save
        _registerBtn = button;
    }
    return _registerBtn;
}

```

### Objective-C 實作原理、注意事項

> 透過 Override 物件的 getter 方法，在 getter 方法裡面去判斷物件是否存在。  
> 若物件不存在 == nil 時則進行物件的初始化，那如果物件已經存在了，則直接return已經初始化完畢的物件。  
>
>所以我們在使用Lazy load的物件時，一定要用 self.變數名稱的方式取值。


---

### Swift 寫法
```javascript=
// 若不進行特殊的初始化，非常簡單，只要前方加上lazy就好
lazy var dataSource = UIButton()

// 若有要進行初始化或邏輯處理，也不複雜
lazy var registerBtn: UIButton = {
    let button = UIButton()
    button.setTitle("註冊", for: .normal)
    button.addTarget(self, action: #selector(registerBtnClick), for: .touchUpInside)
    return button
}()
```

### Swift 實作原理、注意事項

>在 Swift 中 Lazy load 只能用 var 來宣告，因為 let 常數必須在宣告的時候就賦值。  

>還有一個點要注意，就是在Objective-C中可以將lazy變數設置為nil，就可以再重新初始化。  
>在Swift & Objective-C中的nil代表的是不同的意義，在OC中代表空指針，而在Swift中代表空值，所以在Swift中變數=nil時，就等於直接給他賦值nil，所以使用該變數時也不會像OC一樣再進行一次初始化了。