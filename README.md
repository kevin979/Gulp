---
description: '[Gulp](https://gulpjs.com/) 筆記'
---

# Gulp

### 介紹

Gulp 是一個自動化工具，基於串流(Stream)的方式來完成所設立的任務(Task)，擁有豐富的套件可供使用，可滿足前端開發者大部分的需求。

某些網站的代碼全部都擠在一起，這個應用叫做壓縮代碼，通常在將要正式上線的網站都會有壓縮代碼的步驟，原因很簡單，以網站效能來說，大小的控管是非常重要的，多餘的空間就足以造成整體效能的低落，而壓縮代碼這個服務就是依靠Gulp、Webpack等相關工具來完成的。

Gulp 不只可以幫助我們完成壓縮代碼服務，其中也包含以下功能：

* 編譯 Sass、Pug、CoffeeScript 等預處理器
* 壓縮 HTML、CSS、JavaScript 代碼以及各類圖檔
* 建立具有 Livereload 的 Web Server
* 一鍵將專案部署到 GitHub Page

### 安裝

Gulp 依賴 Node.js 環境，需先進行安裝。推薦使用 LTS 版本：

* LTS：長期維護版(較穩定)
* Current：目前最新版(較為不穩定)

安裝完後可在終端機中下指令確認是否安裝成功，如有出現版本號表示安裝成功！

```bash=
node -v
npm -v
```

安裝Gulp時區分全域安裝與區域安裝：

* 全域安裝：代表本機環境
* 區域安裝：僅安裝在專案上

網路上教學都是採用全域安裝，但如果不同專案採用不同版本，怕會有狀況！

因此在這裡我先採用區域安裝，如果區域安裝可執行，那全域安裝就沒有其必要性！

事實證明，全域安裝必裝，否則gulp指令無法使用

一樣，在終端機中輸入指令

```bash=
#先將位置移到專案目錄底下
D:
cd D:\workspace\gulp demo
#初始專案(各選項可填可不填，若不填則一直按Enter忽略即可)
npm init
#安裝Gulp 3.9.1版本
npm install gulp@3.9.1
```

初始完後專案目錄底下會有package.json，內容則是初始時的選項

而Gulp安裝完後會專案目錄底下會有node\_modules資料夾以及package-lock.json

`P.S 如不指定gulp版本，則會安裝最新版，但事後可以指定版本重新安裝！`

### 開始第一個Task

新增 `index.html` 與 `gulpfile.js` 這兩個檔案

```
gulp-demo/
│
├─── node_modules/
├─── index.html           # HTML 主檔案(複製用)
├─── gulpfile.js          # Gulp 配置檔案
├─── package-lock.json
└─── package.json         # 安裝 gulp
```

`gulpfile.js`是 Gulp 專屬的配置檔案，所有的 Task 任務都是在這邊編寫完成

```javascript=
const gulp = require('gulp');

gulp.task('default', () => {
    console.log("hello gulp ~!")
});
```

接著在目標資料夾底下使用 cmd (命令提示字元)

```bash=
gulp default
```

然後就出現錯誤了！！！？

```bash=
ReferenceError: primordials is not defined
    at fs.js:45:5
    at req_ (D:\workspace\gulp demo\node_modules\natives\index.js:143:24)
    at Object.req [as require] (D:\workspace\gulp demo\node_modules\natives\index.js:55:10)
    at Object.<anonymous> (D:\workspace\gulp demo\node_modules\graceful-fs\fs.js:1:37)
    at Module._compile (internal/modules/cjs/loader.js:1063:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
    at Module.load (internal/modules/cjs/loader.js:928:32)
    at Function.Module._load (internal/modules/cjs/loader.js:769:14)
    at Module.require (internal/modules/cjs/loader.js:952:19)
    at require (internal/modules/cjs/helpers.js:88:18)
```

經確認，電腦安裝的node.js版本是v14.16.1，gulp版本是v3.9.1

因為node.js太新而gulp太舊導致無法運行！解決方法：

* node.js降版至v10 ===>我選擇此法
* gulp升級至v4

再執行一次，出現以下畫面，表示成功囉

```bash=
D:\workspace\gulp-demo>gulp default
[17:20:36] Using gulpfile D:\workspace\gulp-demo\gulpfile.js
[17:20:36] Starting 'default'...
hello gulp ~!
[17:20:36] Finished 'default' after 701 μs
```

再新增一個task，目的是複製檔案！

```javascript=
gulp.task('copyFile', () => {
  // 'copyFile' 是任務名稱，可自行定義
  return gulp.src('./index.html').pipe(gulp.dest('./public'));
});
```

* gulp.task()：創建名為 copyFile 的任務
* gulp.src()：導入 index.html 這一個檔案
* .pipe()：以 gulp.src() 導入的檔案需做的處理
* gulp.dest()：檔案輸出的目錄

然後執行`copyFile`

```bash=
D:\workspace\gulp-demo>gulp copyFile
[09:19:56] Using gulpfile D:\workspace\gulp-demo\gulpfile.js
[09:19:56] Starting 'copyFile'...
[09:19:56] Finished 'copyFile' after 16 ms
```

此時會發現gulp copy了index.html到指定目錄底下！資料夾結構如下：

```bash=
gulp-demo/
│
├─── node_modules/
├─── public/
│   │
│   └─── index.html       # HTML 副本 (完成編譯)
│
├─── index.html           # HTML 主檔案(等待編譯)
├─── gulpfile.js          # Gulp 配置檔案
├─── package-lock.json
└─── package.json         # 安裝 gulp
```

### Sass/SCSS 編譯

#### 簡介

> Sass/SCSS 是 CSS 的預處理器，擴充了既有 CSS 的語法、規則及功能，但 .sass/.scss 檔案是無法在Browser運行的，需要透過編譯器使之編譯成 CSS 檔案才能成功運行。

#### 安裝套件

```bash=
npm install gulp-sass
```

僅需要在專案底下安裝即可，安裝過程中，會連同node-sass一起安裝。

安裝完後可在node\_modules資料夾中找到gulp-sass以及node-sass。

#### 範例

新增.scss檔案，此時資料夾結構如下：

```bash=
gulp-demo/
│
├─── node_modules/
├─── public/
├─── source/
│   │
│   └─── scss/
│       │
│       └─── demo.scss     # SCSS 主檔案
│
├─── index.html
├─── gulpfile.js
├─── package-lock.json
└─── package.json
```

撰寫SCSS範例：

```sass=
$color-primary: blue;

body {
  color: $color-primary;
  font-size: 16px;
}
```

修改task檔案(gulpfile.js)

```javascript=
const gulp = require('gulp');
const sass = require('gulp-sass'); // 載入 gulp-sass 套件

gulp.task('buildSass', () => {
  return gulp
    .src('./source/scss/*.scss') // SCSS 主檔案路徑
    .pipe(sass().on('error', sass.logError)) // 使用 gulp-sass 進行編譯
    .pipe(gulp.dest('./public/css')); // 編譯完成輸出路徑
});
```

執行編譯任務

```bash=
D:\workspace\gulp-demo>gulp buildSass
[09:58:00] Using gulpfile D:\workspace\gulp-demo\gulpfile.js
[09:58:00] Starting 'buildSass'...
[09:58:00] Finished 'buildSass' after 21 ms
```

編譯完成後，專案目錄結構就會如下圖：

```bash=
gulp-demo/
│
├─── node_modules/
├─── public/
│   │
│   └─── index.html
│   │
│   └─── css/
│       │
│       └─── demo.css    #編譯完成的 CSS 檔案
│   
├─── source/
│   │
│   └─── scss/
│       │
│       └─── demo.scss     # SCSS 主檔案
│
├─── index.html
├─── gulpfile.js
├─── package-lock.json
└─── package.json
```

編譯後的css檔案內容如下：

```css=
body {
  color: blue;
  font-size: 16px; }
```

#### 補充說明：Dart Sass 與 Node Sass

> Dart Sass 與 Node Sass 都屬於 Sass 的編譯器，Dart Sass 具備編譯輸出為 JavaScript 的能力，目前為 Sass 的主要開發對象，這也代表各種新功能將優先引入；Node Sass 底層使用的是 LibSass，基於 C/C++ 編寫，這使其編譯速度快過 Dart Sass；對於一般開發建議使用 Node Sass，如有新功能的需求，可使用 Dart Sass。

如要更換編譯器，可參考下面步驟：

* 安裝 Dart Sass

```bash=
npm install dart-sass
```

* 更改編譯器為 Dart Sass

```javascript=
const gulp = require('gulp');
const sass = require('gulp-sass'); // 載入 gulp-sass 套件

sass.compiler = require('dart-sass'); // 將編譯器調整為 Dart Sass
```

* 執行編譯

```bash=
D:\workspace\gulp-demo>gulp buildSass        
[10:47:39] Using gulpfile D:\workspace\gulp-demo\gulpfile.js
[10:47:39] Starting 'buildSass'...
[10:47:39] Finished 'buildSass' after 77 ms
```

* 編譯後CSS內容如下：

```css=
body {
  color: blue;
  font-size: 16px;
}
```

### Pug編譯

#### 簡介

> Pug 原來的名稱為 Jade，是一套 HTML 的模板語言，你可以把它想像成 CSS 與 SCSS 的關係，Pug 可以幫助開發者簡化語法和模組化開發，一樣需要透過編譯器使之編譯成 HTML 才能正常運行

#### 安裝套件

```bash=
npm install gulp-pug
```

#### 範例

先整理資料夾，移除public & index.html，並新增index.pug，此時資料夾結構如下：

```bash=
gulp-demo/
│
├─── node_modules/
├─── source/
│   │
│   └─── index.pug
│   │
│   └─── scss/
│
├─── gulpfile.js
├─── package-lock.json
└─── package.json
```

撰寫pug內容：

```htmlembedded=
<!DOCTYPE html>
html(lang="tw")
    head
        meta(charset="UTF-8")
        meta(name="viewport", content="width=device-width, initial-scale=1.0")
        title Document
    body
        .container
            .title 測試 Pug
```

修改task檔案(gulpfile.js)

```javascript=
const pug = require('gulp-pug'); // 載入 gulp-pug 套件

gulp.task('buildHtml', () => {
    return gulp
        .src('source/**/*.pug') // Pug 主檔案路徑
        .pipe(pug()) // 使用 gulp-pug 進行編譯
        .pipe(gulp.dest('./public/')); // 編譯完成輸出路徑
});
```

執行編譯任務

```bash=
D:\workspace\gulp-demo>gulp buildHtml
[11:20:48] Using gulpfile D:\workspace\gulp-demo\gulpfile.js
[11:20:48] Starting 'buildHtml'...
[11:20:48] Finished 'buildHtml' after 49 ms
```

編譯完成後，專案目錄結構就會如下圖：

```bash=
gulp-demo/
│
├─── node_modules/
├─── public/
│   │
│   └─── index.html
│
├─── source/
│   │
│   └─── index.pug
│   │
│   └─── scss/
│
├─── gulpfile.js
├─── package-lock.json
└─── package.json
```

編譯後的html檔案內容如下：

```htmlembedded=
<!DOCTYPE html><html lang="tw"><head><meta charset="UTF-8"/><meta name="viewport" content="width=device-width, initial-scale=1.0"/><title>Document</title></head><body><div class="container"><div class="title">測試 Pug</div></div></body></html>
```

可以發現編譯後的html內容有進行壓縮！

原因是gulp-pug的參數pretty預設是false，若不要html是壓縮過後的狀態，則調整參數為true即可。

```javascript=
gulp.task('buildHtml', () => {
    return gulp
        .src('source/**/*.pug') // Pug 主檔案路徑
        .pipe(pug({
            pretty: true, //將參數調整為true
        })) // 使用 gulp-pug 進行編譯
        .pipe(gulp.dest('./public/')); // 編譯完成輸出路徑
});
```

編譯後的html檔案內容如下：

```htmlembedded=
<!DOCTYPE html>
<html lang="tw">
  <head>
    <meta charset="UTF-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Document</title>
  </head>
  <body>
    <div class="container">
      <div class="title">測試 Pug</div>
    </div>
  </body>
</html>
```

### 自動清除檔案與資料夾

#### 前言

每次在進行gulp進行自動化操作之前都必須手動刪除編譯後的內容，避免之前的編譯結果干擾！

但是手動刪除又違背了自動化的宗旨，因此需要自動化清理！

#### 安裝套件

```bash=
npm install del
```

#### 範例

此時的資料夾結構如下圖：

```bash=
gulp-demo/
│
├─── node_modules/
├─── public/
│   │
│   └─── index.html
│
├─── source/
│   │
│   └─── index.pug
│   │
│   └─── scss/
│
├─── gulpfile.js
├─── package-lock.json
└─── package.json
```

修改task檔案(gulpfile.js)

```javascript=
const del = require('del'); // 載入 del 套件

gulp.task('clean', () => {
    return del(['./public']); // 需刪除檔案或目錄
});
```

執行任務

```bash=
D:\workspace\gulp-demo>gulp clean
[15:56:42] Using gulpfile D:\workspace\gulp-demo\gulpfile.js
[15:56:42] Starting 'clean'...
[15:56:42] Finished 'clean' after 15 ms
```

執行完後public資料夾被刪除了，此時資料夾結構就會如下圖：

```bash=
gulp-demo/
│
├─── node_modules/
│
├─── source/
│   │
│   └─── index.pug
│   │
│   └─── scss/
│
├─── gulpfile.js
├─── package-lock.json
└─── package.json
```

在編譯時，都會將舊的檔案刪除再進行編譯，因此task需依序執行，以下就是多task執行語法

```javascript=
// gulp3語法
gulp.task('build', ['clean', 'buildSass', 'buildHtml']);

// gulp4語法
gulp.task('build', gulp.series('clean', 'buildSass', 'buildHtml'));
```

執行任務

```bash=
D:\workspace\gulp-demo>gulp build
[16:01:32] Using gulpfile D:\workspace\gulp-demo\gulpfile.js
[16:01:32] Starting 'clean'...
[16:01:32] Starting 'buildSass'...
[16:01:32] Starting 'buildHtml'...
[16:01:32] Finished 'clean' after 27 ms
[16:01:32] Finished 'buildHtml' after 113 ms
[16:01:32] Finished 'buildSass' after 122 ms
[16:01:32] Starting 'build'...
[16:01:32] Finished 'build' after 32 μs
```

另外，del可搭配`.gitignore`文件進行檔案忽略！

```javascript=
/* --- 刪除指定目錄檔案 -- */
gulp.task('clean', () => {
    return del(['./public'], {
        gitignore: true, //開啟gitignore設定
    }); // 需刪除檔案或目錄
});
```

