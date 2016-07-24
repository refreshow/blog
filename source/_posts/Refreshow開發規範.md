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
    - 範例：新增Facebook登入功能、專案整合css modules、大規模的Code Refactor

  - 不需要Feature Branch的情況
    - 只需要一次Commit就可以完成的異動
      請直接Commit在`dev` Branch
    - 範例：無法歸類於某個Feaure的小Bug或Typo

  - Feature Branch命名
    - 使用dash（`-`）串聯英文單字，盡量全部以`名詞`組合而成
    - 不必以`feature-`開頭

## Git的使用

- Commit Message
  - 盡量英文
  - 首行首字大寫

- Commit前檢測Coding Style
  此部分若是使用Terminal操作Git，已經設定好Hook強制執行了，若是使用GUI，請手動執行
  ```
  npm run jscs
  ```

- Push前
  - 使用Rebase整理Commits
  - 執行測試
    將local commits push至`dev branch`之前，必須執行
    ```
    npm test
    ```
    確保dev永遠是production ready的狀態。

- Merge
  為增加git log可讀性，所有的merge都要加上`--no-ff`，請勿使用GUI進行merge，範例如下：
  ``` bash
  git merge --no-ff "branch-name"
  ```

## Coding Style

- preset採用[node-style-guide](https://github.com/felixge/node-style-guide)
- 每行限制80字元
- 檔尾需要保留一行空白行
- Components
  - 傳遞靜態屬性使用雙引號（`"`），請勿使用單引號（`'`）

## Modules的Import順序

1. node內建的modules
2. 安裝於node_modules內的modules
  有些community modules內建Components，視同此類型
3. src下的modules
  1. Constants
  2. APIs
  3. Actions
  4. Components
  5. 其他

範例：

``` js
import React, { Component, PropTypes } from 'react'; // 2
import classnames from 'classnames'; // 2
import orderStatus from '../../../constants/status/order'; // 3-1
import orderAPI from '../../../api/order'; // 3-2
import PageLayout from '../../layouts/PageLayout'; // 3-4
import Head from '../../Head'; // 3-4
import PageHeader from '../../main/PageHeader'; // 3-4
import OrderAppliedEmployee from '../../OrderAppliedEmployee'; // 3-4
import OrderOverview from '../../OrderOverview'; // 3-4
import PhaseList from '../../PhaseList'; // 3-4
import Only from '../../utils/Only'; // 3-4
```

## 撰寫Component的注意事項

- js的字串一律使用單引號或backtick（`` ` ``）
- html的props一律使用雙引號
