title: Refreshow開發規範
date: 2016-07-21 02:14:58
tags:
categories: training
author: gocreating
---

Refreshow專案開發規範，提供新進開發人員開發時的注意事項，及開發團隊應遵守的開發原則。

<!-- more -->

## Branch

  - Branching Model採用`Gitflow`
  - 需要建立Feature Branch的情況
    - `新增產品功能`或`移除產品功能`
      若要`更新產品功能`，優先使用已存在的Feature Branch，若不存在才建立新Branch
    - 你覺得必須開出Branch才能完成的功能
    - 範例：新增Facebook登入功能、專案整合css modules

  - 不需要Feature Branch的情況
    - 只需要一次Commit就可以完成的異動
      請直接Commit在`dev` branch
    - 範例：無法歸類於某個Feaure的小Bug

  - Feature Branch命名
    - 使用dash（`-`）串聯英文單字，盡量全部以`名詞`組合而成
    - 不必以`feature-`開頭

## Git的使用

- Commit message
  - 盡量英文
  - 首行首字大寫

- Push前執行測試
  將local commits push至`dev branch`之前，必須執行
  ```
  npm test
  ```
  確保dev永遠是production ready的狀態。

- Commit前檢測Coding Style
  此部分若是使用Terminal操作Git，已經設定好Hook強制執行了，若是使用GUI，請手動執行
  ```
  npm run jscs
  ```

- Merge
  為增加git log可讀性，所有的merge都要加上`--no-ff`，請勿使用GUI進行merge，範例如下：
  ``` bash
  git merge --no-ff "branch-name"
  ```

## Coding Style

- preset採用[node-style-guide](https://github.com/felixge/node-style-guide)
- 每行限制80字元
- 檔尾需要保留一行空白行
