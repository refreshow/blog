title: 如何多人協作Github Pages部落格
date: 2016-07-17 15:43:09
tags:
  - hexo
  - blog
categories: training
author:
  - gocreating
  - rae
---

多數人經營Github Pages當作自己的`個人`部落格，但是如果是公司、組織或團隊需要`多人`協作一個技術部落格呢？我們以hexo框架為例帶您了解Refreshow部落格是如何管理的！

<!-- more -->

## 分支

管理部落格比開發產品簡單多了，我們只需要`master`與`gh-pages`兩個branches。

- `master`
  用來存放hexo的`source code`，包刮主題檔、文章、頁面、草稿等資料。無論是對部落格進行何種操作，包刮撰寫文章、更動主題，都應該使用此branch。

- `gh-pages`
  用來存放hexo建置出來的檔案，我們從頭到尾都不應該操作到此branch，只有在執行`hexo deploy`時，會經由hexo操作到此branch。

## 建立文章

``` bash
hexo new post "文章標題"
```

## Local端預覽

正式發布文章之前，我們可以在Local端預覽一下，確認沒有問題再發布。當然為求保險，此階段也可以直接Commit文章到master，這並不會影響到部落格本身。

``` bash
hexo generate --watch
hexo server --debug
```

## 發佈至Github

由於我們是多人協作，為了避免發佈時覆蓋掉其他作者的文章，記得要先把其他人的文章pull下來，然後重新產生靜態檔案，最後才能Deploy。

``` bash
git pull
hexo generate
hexo deploy
```

## 階層式分類

如果你想要寫的文章，需要兩層式以上來分類，則需要改動_config.yml裡面的permalink
例如，如果你想要寫一個React.js的文章，他隸屬Javascript的範疇，則需要在_config.yml中，改成
``` bash
permalink: :category/:title/
```
三層以上，以此類推..

然後在自己文章中的開頭寫成
```
categories: [Javascript, React.js]
```
這樣就大功告成

## 文章版面

你可以決定你的文章要顯示多少在首頁上，只需要在你想顯示的部分的下面打上即可。
```
<!--more -->
```

## 備註

1. hexo的指令也可以使用簡寫：

  ``` bash
  hexo g -w # hexo generate --watch
  hexo s # hexo server
  hexo d # hexo deploy
  ```

2. 自動化

  可以在`package.json`新增腳本

  ```js
  {
    // ...
    "scripts": {
      "deploy": "git pull && hexo generate && hexo deploy"
    },
    // ...
  }
  ```

  之後只要`npm run deploy`就能發佈囉！
