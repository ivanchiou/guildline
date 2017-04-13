# EIT F2E Javascript Modular Code Style 
*東森訊息科技前端團隊 Javascript Modular 模組化程式碼參考準則*

## 目錄

1. [模組化優點](#模組化優點)
1. [模組方式](#模組方式)
1. [公私分明](#公私分明)
1. [分類規則](#分類規則)
1. [模組初始化](#模組初始化)

## 模組化優點
* 將程式模組化，方便成員間使用同樣的命名讀取與顯示。
* 設定 Public & Private function 可增加 Javascript 的安全性。
* 將功能分類，能快速找到程式錯誤，並集中邏輯思考。

## 模組方式
建置方式需包覆在 function 內，請勿直接公開模組至公用變數

```js
(function() {
    ... // 模組程代碼撰寫區域
})();
```
宣告模組 function 名稱可以使用 **Manager**, **Helper**, **Component**, **Modular** 結尾，依網站需求而決定統一名稱

```js
(function() {

    // 宣告模組 function 處理側滑選單
    function SlideMenuManager() {
        ...
    }

    // 宣告模組 function 處理 Header 區塊模組
    function HeaderModular() {
        ...
    }

    // 
})
```

## 公私分明

特定的公開接口變數 **that**
特定的私有接口變數 **mgr**
```js
(function() {
    function SlideMenuManger() {
        var that = this; // 代表 manager 本身
        var mgr = {}; // manager 內部存放私有函數的變數

    }
})
``` 
公開函數寫法
```js
(function() {
    function SlideMenuManger() {
        var that = this;

        that.init = {
            menu: function() {
                // 初始化側滑選單的資料及動作
            }
        }
    }
})
```
私有函數寫法
```js
(function() {
    function SlideMenuManger() {
        var mgr = {};

        mgr.ajax = {
            getMenuItems: function() {
                // 取得側滑選單的資料
            }
        }

        mgr.display = {
            menuItems: function() {
                // 顯示側滑選單的樣式
            }
        }
    }
})
```


## 分類規則

Javascript 常用的函式命名規則

- 非同步讀取資料 **ajax**
* 取得資料 **get**
* 設定資料 **set**
* 傳送資料 **post**
* 更新資料 **update**
* 刪除資料 **delete**

- 初始/顯示元件 **init**, **display**, **render**
* 元件名稱 **menu** 

- 處理事件 **event**, **handle**
* 元件名稱+動作 **menuClick**, **inputChange**

儘可能不要將**非同步讀取資料**及**顯示元件**設定為Public，增加程式的安全性，下列程式為應用的範例：

```js
(function() {
    function SlideMenuManger() {
        var that = this;
        var mgr = {};

        //Public Methods
        that.init = {
            menu: function() {
                mgr.ajax.getMenuItems();
            }
        }

        //Private Methods
        mgr.ajax = {
            getMenuItems: function() {
                $.ajax({
                    url: '...',
                    success: mgr.display.menuItems
                });
            }
        }

        mgr.display = {
            menuItems: function(json) {
                // 顯示側滑選單的樣式
            }
        }
    }
})
```

## 模組初始化

可以直接於模組的下方宣告，也可以放在jQuery Document ready 時初始宣告。

* 方法一
```js
(function() {
    function SlideMenuManger() {
        ...
    }

    var slideMenuManger = new SlideMenuManger();
    slideMenuManger.init();

})
```

* 方法二 - jQuery
```js
(function() {
    function SlideMenuManger() {
        ...
    }

    $(docuemnt).ready(function() {
        var slideMenuManger = new SlideMenuManger();
        slideMenuManger.init();
    });

})
```

若需公開特定函數給外面的模組使用，推薦binding 於 $ 字符

```js
// slidemenuManger.js
(function() {
    function SlideMenuManger() {
        ...
    }

    $(docuemnt).ready(function() {
        var slideMenuManger = new SlideMenuManger();
        $.initMenuItems = slideMenuManger.init;
    });

})
```

```js
// others.js
...
$.initMenuItems();
...
```