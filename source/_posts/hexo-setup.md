---
title: hexo 官網教學是不是有一點雷
date: 2019-12-01 01:15:42
tags: hexo
---

## hexo 官網教學是不是有一點雷

https://hexo.io/docs/github-pages

上面連結那頁的內容似乎根本不能用，搭配 Travis CI 很棒棒是沒錯，但是我沒辦法做到第 10 點將 GitHub Pages 的 Source 給切換去用 gh-pages branch

也可能是我有什麼環節弄錯，總之最後我用以下的方式來做

## 安裝

```bash
npm install -g hexo-cli

d:

hexo init blog

cd blog

npm i

git init
```

---

這時到 github 蓋一個 `<這邊是你的帳號>.github.io` 的 Repository

也蓋一個 `blog` 的 Repository

共兩個，一個放編譯後的靜態檔案(`<這邊是你的帳號>.github.io`)，一個放未編譯前的檔案(`blog`)

---

回到專案資料夾

git remote add origin https://github.com/<這邊是你的帳號>/blog.git

調整 _config.yml 的內容，約第 97 行

```yml
deploy:
  type: git
  repo: https://github.com/<這邊是你的帳號>/<這邊是你的帳號>.github.io.git
  branch: master
```

然後到 package.json 增加一個新的 script，把 清除檔案 + 產生靜態檔案 + 部屬到目標 的指令合成一行

```json
"start": "hexo clean && hexo generate && hexo deploy"
```

```bash
npm run start
```

新增文章時要輸入

```bash
hexo new <檔案名稱>
```

文章打完後，就一行剛剛特別寫的 `npm run start`

就會依照 _config.yml 寫的目標來 deploy 上靜態檔案
