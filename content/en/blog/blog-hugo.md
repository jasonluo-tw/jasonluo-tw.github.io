---
author: "Jason Luo"
title: "利用Hugo+Github Page打造個人部落格"
description: "How to use Hugo & GithubPages to build my blog"
tags: ["web", "tech"]
date: 2022-03-17
thumbnail: https://images.unsplash.com/photo-1507842217343-583bb7270b66?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1590&q=80
---
&nbsp;&nbsp;2022年，一個新年新希望就是重啟寫blog的計畫，希望藉由寫些文章，不只可以好好的紀錄下自己的學習歷程，同時也可以把文章當成是備忘錄，以後當忘記某個曾經看過或學過的東西的時候，就可以直接回來翻之前寫過的文章。當然，也可以同時練練自己的文筆，甚至是有朋友問起的時候，就直接把文章的link帥氣的丟給他😎
&nbsp;&nbsp;鑑於以上種種原因，所以今年我就重新開始花時間思考要寫什麼，同時呢也把blog重新整理了一下。其實大概在兩三年前有用過Hexo建過blog，但之後就幾乎沒什麼去碰它。現在重新回過頭來整理的時候，發現很多東西都有點遺忘，所以就利用這篇文章記錄一下一些重要的點。而這次我也直接把部落格從Hexo轉移到Hugo上，雖然現在大致上有一個樣子了，但很多東西我會慢慢再加上去，希望未來慢慢可以變成一個很酷的blog🙌
<!-- more -->

> 以下就開始進入正題吧

## 大綱
- Hugo網站整體架構
- 將網站放到github pages上
- github Action的使用
- 使用個人網域名(Godaddy)


## Hugo網站整體架構
### 下載Hugo，開啟新的Hugo專案
&nbsp;&nbsp;既然已經決定要用Hugo當成部落格框架，當然第一件事就是去逛他的[官網](https://gohugo.io/)並下載安裝Hugo，Linux Ubuntu的安裝方法就是
```
sudo apt-get install hugo
```
成功下載了之後就可以來創建一個新的Hugo blog，只要下個指令就可以快速生成一個新的專案
```
hugo new site <資料夾名稱>
```
之後進到這個資料夾後會看到這些檔案or資料夾([官網有介紹各個資料夾的用途](https://gohugo.io/getting-started/directory-structure/))
> archetypes -> 放置一些md文章的模板
> content -> 文章放置的位置
> data 
> layouts
> static -> 靜態檔案放置的位置(如圖片檔)
> themes -> 部落格使用的主題放置的位置
> config.toml -> 一些參數的設定檔

### 部落格主題
接下來就來挑選想要的blog主題，[官網](https://themes.gohugo.io/)上有整理出別人寫的一些主題，可以先從裡面挑一個喜歡的，如果有餘力之人之後可以再慢慢修改主題裡面的一些內容，讓自己的blog變得獨一無二！
挑選好主題之後先在資料夾裡`git init`，之後再使用`git submodule`把主題加到`themes`資料夾底下。這邊我選擇使用的主題是[blist](https://themes.gohugo.io/themes/blist-hugo-theme/)，所以就可以使用以下指令
```
git submodule add https://github.com/apvarun/blist-hugo-theme.git themes/blist
```
但我比較推薦的方式是先把主題的repo fork回自己的github，這樣以後修改到主題中的程式碼時才可以把這些修改也推送到自己的github上。因此fork回自己的github後，要add的網址是自己repo的網址
```
git submodule add https://github.com/jasonluo-tw/blist-hugo-theme.git themes/blist
```
這樣就成功把主題下載下來了，再來就是修改一些設定檔(config.toml)
> 使用Blist主題的話，因為它會用到一些npm上的package，所以要使用到npm install
> 1. 先把`package.json`和`package-lock.json`(在themes/blist裡)複製到blog專案的根目錄
> 2. 回到專案的根目錄執行`npm install`

### 修改設定檔
接下來是修改config.toml這個檔案。
首先，要把主題改成blist，打開config.toml並在裡面加入一行
```
theme = "blist"
```
這樣就把主題改成了blist了。而其他的設定則可以參考`themes/blist/exampleSite`裡的config.toml，同時裡面也有放其他資料夾的範例，最省時間無腦的方式就是直接把exampleSite底下的東西都複製到專案的根目錄。假設都複製好之後就可以直接下
```
hugo serve -D
```
然後用瀏覽器打開`http://localhost:1313/`就可以看到整個網站的樣子了！

<center><img src="https://i.imgur.com/mCv48EF.png" style="width: 500px" /></center>

接下來就可以慢慢修config.toml裡面的內容，把一些資料改成自己的資料🤘🏻

### 第一篇文章
網站的樣子大致有了之後就可以來試著新增一篇文章
```
hugo new posts/hello_world.md
```
使用blist主題的話則是打
```
hugo new blog/hello_world.md
```
hello_world.md檔案會在`./content/en/blog/`底下生成
打開hello_world.md裡面會有以下東西
```
---
title: "Test"
date: xxxxxxx
draft: true
---
```
如果draft是true的話，此篇文章不會被部署到網站上，當整個文章都改好之後把draft改成false就可以順利發布出去。
> 上述自動生成的一些東西，可以在archetypes資料夾裡的default.md做修改

**生成檔案之後就可以mark down語法來打文章啦!**

以上網站的樣子都有了之後，就是把它放到網路上讓別人可以瀏覽。
這邊推薦可以使用github免費提供的github pages，來放置這些檔案。

## Github pages和Github Action
Github有提供免費放置靜態網站的服務，所以我們可以利用這個服務來放置我們自己的Hugo檔案。然後再搭配Github Action做自動部署，這樣就可以把生成的靜態網站跟原始的文章檔分離，之後在別台電腦上只要clone repo下來，寫好文章之後再推上去，就可以直接自動更新網站了
**(同時可以省略需要在本地端要跑npm install及hugo生成靜態檔案的指令)**

### 設定Github page
第一步就是要在自己的Github新增名稱為`<Github的名稱>.github.io`的repo
然後在本地端的blog目錄中加入這個repo的remote位址，並開設一個分支叫`sources`來放置一些靜態圖檔、md檔案，而`main`的分支則是放置部署之後產生的檔案(public資料夾裡的檔案)
```
git remote add origin <repo位址>
git checkout -b sources
```
> 1. 這邊要注意的一點是不要把`node_modules`、`public`的資料夾push上去，所以要用個.gitignore的檔案讓git忽略那兩個資料夾
> 2. 另外一個要注意的點是使用Github pages的網頁，網址會是<username>.github.io，所以要修改一下config.toml，把url改成相對應的網址

之後把剩餘的東西commit完之後推送到Github上
```
git push origin sources
```
接下來就是要設置Github Action來達到自動部署
    
### Github Action
&nbsp;&nbsp;一開始使用blog的時候還沒有Github Action，那時候使用的是[TravisCI](https://www.travis-ci.org/)，但在2021 6/15之後TravisCI不再提供免費的服務，因此我才重新研究了一下Github Action。

首先，我們要先到`setting->developer settings`，產生一個新的token並把workflow的那個選項打勾
![](https://i.imgur.com/gLHXqbd.png)

產生新的token之後，接下來要複製這個token並添加到存放Hugo部落格檔案的repo中，位置是在`Repository -> Settings -> Secret -> New repository secret`，新增剛剛取得的token
![](https://i.imgur.com/DqEnOl3.png)

設置完token之後就可以來寫Github Actions了。在repo的頁面中選擇Github Actions並按下New workflow，並新增以下的code
```yaml
# This workflow is to automatically deploy the Github Pages

name: github pages CD
on:
  push:
    branches:  
    - sources
    schedule:
      - cron: "0 12 ** SUN"

jobs:
  deploy:
    name: Deploy Github Pages
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@v2
      with:
        ref: sources
        token: ${{ secrets.hugo_deploy_token }} # Here is your token name
        submodules: true
        
    - name: Setup Hugo
      uses: peaceiris/actions-hugo@v2
      with:
        hugo-version: '0.92.1'
        
    - name: Setup Node.js
      uses: actions/setup-node@v1
      with:
        node-version: '12.17.0'
        
    - name: Setup theme dependency
      run: npm install
      
    - name: Build new hugo public
      run: hugo
      
    - name: Deploy to the main branch
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.hugo_deploy_token }} # Here is your token name
        publish_dir: ./public
        publish_branch: main
```

**這邊礙於主題是搭建部落格所以不會仔細講Github Action的每一個步驟，有機會之後會再另外寫一篇來整理Github Action**

寫完Github Action之後，接著就看它是否有正常執行，如果一切都順利的話，最後生成的文件會放置在main的分支上。此時最後的步驟是到`Repository -> Settings`內，將Github Pages的source改成main就大功告成了
    
![](https://i.imgur.com/DhGW2j2.png)

*註:此時的網址應為`https://<username>.github.io/`而不是`https://jasonluo.tw/`
*要再設定網域服務才會變成`jasonluo.tw`
    
## 申請個人網域服務
&nbsp;&nbsp;設定完Github Pages之後，此時的網址會是`https://<username>.github.io`，雖然已經足夠讓人家去瀏覽自己的blog，但如果能夠使用更簡短好記的網址的話，更能讓人對你的blog有比較深刻的印象。像是`jasonluo.tw`、`TonyLin.org`...等隨便舉例，都是簡單好記的網址。所以接下來就是要去尋找有提供網域服務的平台，台灣大家常用的網域服務之一就是[GoDaddy](https://tw.godaddy.com/)，以下就用Godaddy為例來簡單介紹怎麼設定自己的網域名稱吧。

首先，到GoDaddy的網站搜尋自己想要的網域名稱，像是打入jasonluo之後就會出現各種有jasonluo的網域名，每個網域名的價格也不太一樣，之後選擇自己喜歡、價格又可以接受的網域名，購買好之後就來弄一些DNS的相關設定。

點選買好的網域往下滑到最下面，可以看到管理DNS的選項，點進去之後我們要來設定[A和CNAME](https://support.dnsimple.com/articles/differences-a-cname-records/)(不知道A和CNAME的可以點進連結)
    
![](https://i.imgur.com/Wq79mgH.png)


A就是Address，要映射到的IP位址，所以這邊要給的是Github主機的IP位址(185.199.108.153和185.199.109.153是github的主機IP位址)，而CNAME則是要給映射過去的網址，這邊的就話就要給原本Github page的網址`https://<username>.github.io/`，下圖為設定好之後的樣子

![](https://i.imgur.com/cga7Ii7.png)
    
&nbsp;&nbsp;接下來回到Github，我們要來更新github page中的custom domain。回到存放靜態網頁的repo中，選擇`Settings -> github pages -> custom domain`，打入自己的網址，並按下Save儲存更改設定

![](https://i.imgur.com/q7uJQZs.png)
    
最後一步最重要但也很容易忽略的步驟是要新增一個**名稱CNAME的檔案，裡面的內容是自己申請的網域位址，並將這個CNAME檔案放到main的分支中**，才算完成，如果不做這個步驟的話雖然一樣可以用自己的網域位址瀏覽網站，但會出現CORS的問題，網站有可能會不能正常載入一些存放在github repo中的靜態圖檔、css、js檔案，所以這個步驟很重要！

我的做法不是直接將CNAME放到main的分支中，而是將它放到static的資料夾中，因為如果直接放到main的分支中每次Github Action重新deploy完會蓋掉CNAME，而放到static的資料夾中則會在每次deploy完後，CNAME會自動被放到main的分支上
    
以上的步驟做完之後基本上整個網站的建立就大功告成啦🥳🥳🥳

之後如果熟悉js和css的朋友們，可以直接去改source code增加自己想要的功能，讓整個網站看起來更酷、更潮😎
以下是筆者之後想加入的功能，列出來給大家參考:
- disqus留言區
- 改善open graph
- Katex 數學公式輸入
- 中文文字字數統計改善
- 介紹文打字機效果
- 照片3D粒子效果

...等等。有興趣的朋友可以再深入研究，或是常去瀏覽codepen別人做的一些效果，在這過程中可以讓自己學習到更多～

以上就是這次的分享啦！希望能帶給大家一些幫助😁


Ref:
- [Hugo document](https://gohugo.io/documentation/)
- [Hugo theme Blist](https://themes.gohugo.io/themes/blist-hugo-theme/)
- [gitmodule介紹](https://blog.wu-boy.com/2011/09/introduction-to-git-submodule/)
- [Github Action介紹](https://medium.com/starbugs/%E5%AF%A6%E4%BD%9C%E9%96%8B%E6%BA%90%E5%B0%8F%E5%B7%A5%E5%85%B7-%E8%88%87-github-actions-%E7%9A%84%E7%AC%AC%E4%B8%80%E6%AC%A1%E7%9B%B8%E9%81%87-3dd2d70eeb)
- [Hugo blog部署](https://yurepo.tw/2021/03/%E5%A6%82%E4%BD%95%E5%B0%87hugo%E9%83%A8%E8%90%BD%E6%A0%BC%E9%83%A8%E7%BD%B2%E5%88%B0github%E4%B8%8A/)
