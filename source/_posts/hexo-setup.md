---
title: hexo 官網教學架上 github pages 的說明是不是有一點雷
date: 2019-12-01 01:15:42
categories:
- hexo
tags:
- hexo
cover: https://d33wubrfki0l68.cloudfront.net/6657ba50e702d84afb32fe846bed54fba1a77add/827ae/logo.svg
---

## 原先照著 hexo 官網，也就是下面那個連結的說明過程總是失敗 XD

https://hexo.io/docs/github-pages

搭配 Travis CI 很棒棒是沒錯，但是我沒辦法做到第 10 點將 GitHub Pages 的 Source 給切換去用 gh-pages branch

也可能是我有什麼環節弄錯，總之最後我用以下的方式來做

## 安裝

```bash
npm install -g hexo-cli

hexo init blog

cd blog

npm i

# 這邊特別要記得下載這個套件，才有辦法讓 hexo 正常的執行 git 指令
npm i hexo-deployer-git
```

---

## github 操作步驟

這時到 github 蓋一個 `<這邊是輸入你的帳號>.github.io` 的 Repository

![](https://i.imgur.com/PjFgMVE.png)

也蓋一個 `blog` 的 Repository

![](https://i.imgur.com/g2zhYt4.png)

共兩個 Repository

* 一個放`編譯後的靜態檔案`(`<這邊是輸入你的帳號>.github.io`)
* 一個放`未編譯前的檔案`(`blog`)

---

## 專案資料夾操作步驟

回到專案資料夾

git remote add 的路徑是要放`未編譯的檔案`(`blog`)

```bash
git init

git remote add origin https://github.com/<這邊是輸入你的帳號>/blog.git
```

調整 _config.yml 的內容，約第 97 行，此時設定的是編譯後的靜態檔案部屬的位置(`<這邊是輸入你的帳號>.github.io`)

```yml
deploy:
  type: git
  repo: https://github.com/<這邊是輸入你的帳號>/<這邊是輸入你的帳號>.github.io.git
  branch: master
```

然後到 package.json 增加一個新的 script，把 清除檔案 + 產生靜態檔案 + 部屬到目標 的指令合成一行

```json
"release": "hexo clean && hexo generate && hexo deploy"
```

```bash
npm run release
```

此後打完文章只要一行指令就可以等他產生靜態檔案及丟上 github.io

---

## 新增文章操作步驟

新增文章時要輸入

```bash
hexo new <檔案名稱>
```

會自動產生 .md 檔到 source -> _posts 資料夾裡頭

文章打完後，就一行剛剛特別寫的 `npm run release`

就會依照 _config.yml 寫的目標來 deploy 上靜態檔案

成功佈署後記得也要將未編譯的原始檔進行版控
