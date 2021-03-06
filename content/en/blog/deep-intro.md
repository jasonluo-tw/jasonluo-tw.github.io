---
author: "Jason Luo"
title: "淺談正夯的深度學習"
description: "A brief introduction to Deep Learning"
tags: ["DL", "tech"]
date: 2020-05-31
math: true
thumbnail: https://images.unsplash.com/photo-1589254065878-42c9da997008?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1170&q=80
---
## 前言
&emsp;在2016年3月8日到15日期間，全世界的電腦科學家們甚至是全世界的人們都高度關注的一場世界棋賽，但與之前不同的是，這場世界棋賽並不是一般人與人之間的博奕，而是一場人與機器的世紀大對決。隨著AlphaGO降臨於眾人面前並以4:1戰勝人類最頂尖的棋手之一，開啟了大眾與企業對於人工智慧的熱情，也因此引發了一波人工智慧的熱潮。而在往後的日子中，到處都可以聽到**深度學習**的技術又在某一個領域得到重大的突破，但**深度學習**這個看似很新潮又很酷的東西是什麼玩意呢？它到底又厲害在哪裡呢？為什麼能夠短時間席捲好多領域？

<!--more-->
## what is deep learning ?
<center><img src="https://i.imgur.com/8GkS081.png" width="60%"></center><br>

&emsp;其實如果對這名詞有些好奇的人，去網路上google深度學習的圖片將會查到大量類似如上的圖片，圖片中可以看到一堆圈圈，而這些圈圈之間由很多的線所連接。可能看完上圖大家還是會覺得一頭霧水不知道這個東西到底是什麼，但如果將下圖擺在一起看時就會發現上圖和人類的神經網路有些相似。<br>

<center><img src="https://i.imgur.com/ApbR1PP.png" width="50%" style="margin:auto;"></center><br>

&emsp;對！沒錯，深度學習某一程度其實模仿了人類身體中的神經網絡，圖中的一個一個圈圈代表了一個個神經元，而線則代表了神經元和神經元之間的連接(類似神經中的突觸)。此外，從圖中可以看到這些圈圈有分層，黃色的圈圈為輸入層(Input layer)，中間許多的藍>色圈圈被稱為隱藏層(hidden layer)，最後的紅色圈圈則為輸出層(output layer)，這樣一個架構是現在最基本的深度學習模型。然而，這個模仿人體神經網絡的領域早在1950、1960年代就已經出現，當時這方面相關的研究也被學術界稱為『類神經網路』(Artificial neural network)，在1954年時，弗蘭克羅森布拉特(Frank Rosenblatt)提出『感知機』(Perceptron)的概念，而現在某些書上也可以看到多層感知機(Multi-Layer Perceptron，MLP)其實講的就是同一件事(Dnn)，所以深度學習並不像很多人所想像那樣突然橫空出世，而是已經經過很多時間的醞釀，蟄伏了很長一段時間。

## 怎麼以前沒有 現在有

&emsp;看到這邊大家一定會想問，既然這些概念、演算法很早就已經出現了，為什麼現在才
整個蹦出來出現在大家眼前？其實深度學習能夠發展起來都要歸因於互聯網、大數據以及現
今電腦強大的運算能力。首先，這些網路要給眾人使用以前要先經過大量的訓練，而這些訓
練過程中最不可或缺的即是品質良好的數據資料。舉最基本的手寫數字(Mnist)辨識來說，要讓這些機器能夠分辨照片中的數字到底是多少，我們要先讓機器看過許多不一樣的照片並且跟它說照片中的數字到底是多少，這個過程蠻像人類小時候的學習過程。<br>

<img src="https://i.imgur.com/jvSsKm1.png" width="20%">

&emsp;還記得我年代久遠的小時候，爸爸媽媽會給我很多卡片，像是某張卡片上面畫了一顆
蘋果，然後卡片下面就會寫"蘋果"這兩個字。人們透過這樣幾張的字卡似乎就能很快學會蘋
果這個東西，下次看到的時候雖然可能跟字卡上的蘋果有些差異，但是往往能正確的指出那
個就是一顆蘋果。


<center><img src="https://i.imgur.com/bfcU4c0.png" width="50%"></center>


&emsp;但是如果是利用深度學習的技術時只透過一丁點的資料是沒辦法辨別出巫婆手上的那
顆蘋果的，其實這也是深度學習遇到的問題，在訓練的時候必須有海量資料的幫忙，網路才
能夠訓練的很好，也因此在早期難以收集資料集的情況下，類神經網路的技術是很難勝過其
他傳統的機器學習演算法的。所幸互聯網以及處理大數據的技術的出現，人們在收集資料上
變得比以前簡單很多。下圖為2009年到2020年IDC預測的數位資料量，可以看到資料的成長是非常的劇烈，幾乎是以指數的方式成長，因此，在未來不會沒有資料可以進行訓練，問題只會變得要不要花時間去收集並管理資料而已。


<center><img src="https://i.imgur.com/qX0pdPl.png" width="70%"></center>

&emsp;除了在訓練時需要龐大的資料外，電腦的運算能力在早期也是一大難題。在以前只有
CPU的時代，只要把網路加深幾層或是神經元數加大，就會增加大量的訓練時間，因此要訓>練一個複雜的神經網路往往需要數天、數個禮拜甚至數年。然而，隨著GPU、TPU、叢集(cluster)的出現，大大提昇了電腦的平行運算能力，人們不再像以往需要花大量時間訓練簡單>的網路，同時訓練一個非常複雜的網路也變得較為容易，現在的深度學習研究者如果沒有GPU的話根本不用玩。如果你問我說GPU和CPU的差別在哪，那我會推薦你去看youtube上一個我覺得有趣又短的影片。

<center><iframe width="600" height="400" src="https://www.youtube.com/embed/-P28LKWTzrI" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe></center>

## 學習方法(機器學習)

&emsp;一般機器學習領域中大致上可以概括成**三類**學習方式-**監督式學習(Supervised learning)**、**非監督式學習(Unsupervised learning)**、**強化學習(Reinforcement learning)**，還有一種是介於這監督與非監督之間的**半監督式學習(semi-supervised learning)**，在這邊就討論最前面兩種學習方式。

### 監督式學習

&emsp;所謂**監督式學習**就是機器在學習的過程中有正確答案(label data)可以對照。舉例來說，就是大人帶小孩到動物園玩時，大人常常會指著動物跟孩子說這是甚麼樣的動物，小孩子從他看到的這些動物的特徵(feature)中學會辨別動物們，像是看到灰色、很大隻、>長鼻子、大耳朵的就知道會是一隻大象，而有很長的脖子的通常就知道是長頸鹿。同樣的，機器也是這樣藉由反覆看到資料以及答案，從中尋找資料中的一些特徵，進而學會有甚麼樣特徵的資料會得到甚麼樣的答案。然而，在看似和人同樣的學習方式之下，機器所學到的東西往往會和人們學習到的會有所差異，機器常學到一些奇怪的東西導致在判斷的時候會判斷出一些非常奇怪的結果，有興趣的人可以看看以下的影片。

<center><iframe width="600" height="400" src="https://www.youtube.com/embed/M2IebCN9Ht4" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe></center><br>

### 非監督式學習
&emsp;接下來，非監督式學習其實就是在訓練過程中訓練資料沒有答案(No label)，在這個情況下，通常都是使用**分群**的方法將資料進行分類，常見的有k-means、DBSCAN、Hierarchical clustering等等，有一個不錯的網站，它將[DBSCAN演算法做視覺化的呈現](https://www.naftaliharris.com/blog/visualizing-dbscan-clustering/)有興趣的可以到這個>網站去看看。

&emsp;除了上述說的**分群**方法之外，近幾年在非監督式學習中出現了一個非常熱門的技
術叫做**GAN (Generative Adversarial Networks)**，最早由Ian Goodfellow在2014年提>出，這個技術提出後受到大眾的關注，陸續幾年出現了一堆GAN的變形，Cycle GAN、WGAN、
BEGAN、DRAGAN(我第一次看看成恐龍XD)......以下是摘自gan-zoo的圖，這圖統計了這幾年
來與GAN相關的論文數量，可以看到2017~2018年與GAN相關的論文數量成長非常驚人。

<center><img src="https://i.imgur.com/uzv0ccz.png"></center>


深度學習三巨頭之一 Yann LeCun也對GAN的出現讚譽有加。

<center><img src="https://i.imgur.com/BRR9hCH.png" width="80%"></center>

&emsp;現在網路上有很多畫風轉移的技術很多就有應用到GAN的技術，利用GAN的技術可以輕
鬆的把一個照片轉移成梵谷的畫風，或是把一般的馬轉變成斑馬，甚至之前在Reddit上出現
deepfakes的用戶使用GAN的技術將A片女主角的臉重新換成好萊屋女星的臉，這些噱頭十足>的用途都大大提升了GAN的知名度。

### 強化學習
&emsp;所謂的強化學習，指的是讓機器(Agent)與一個虛擬的環境(Environment)互動，機器
因為環境的狀態而選擇採取某一種行動，而機器會因為這個行動獲得一個分數(Reward)，這
個分數有可能是正或是負的，同時環境會因為機器採取的行動而改變，環境改變之後機器要
重新看這個環境之後再根據環境狀態採取行動...一直循環，而在這些輪迴中機器要想辦法>使獲得的分數(Reward)越高越好。下面這張圖就是強化學習機器與環境互動的圖。

<center><img src="https://i.imgur.com/cB1W96s.png" width="50%"></center><br>

&emsp;上述的過程很像是打遊戲的過程，也因此強化學習常常被用來讓機器學習玩遊戲或是
下棋，最有名的例子就是AlphaGO了，下面是一段使用強化學習中的Q-learning的技術讓機>器能夠自己玩打磚塊，從影片中可以看到剛開始，機器就像一個笨蛋一樣，但是過了120分>鐘的訓練之後，機器玩起打磚塊就像一個專家一樣厲害XD

<center>
<iframe width="560" height="315" src="https://www.youtube.com/embed/V1eYniJ0Rnk" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
</center><br>

## 結語
&emsp;這篇文章非常簡單的介紹了深度學習的背景以及機器學習的方法，但其實還有**非常非常**多的東西值得拿出來與大家討論，像是梯度下降、反向傳播、CNN、RNN、seq2seq......，以及這項技術如何被廣泛應用在各個領域之中。像是深度學習往往會與另一個領域結>合，
像是如果要把聊天機器人的好的話就要對語言學有一些認識，或是想把深度學習應用在文本搜尋系統可能就要對詞向量、tf-idf等要有所了解。如果對這些東西非常有興趣的其實網路上有很多的文章可以看，希望大家能夠在挖掘這些資料海時，能在過程中獲得許多成就感以及快樂～～

***最後推薦大家入坑方向XDD***
&emsp;如果對深度學習的歷史、演進或是想了解人工智慧到底會不會毀滅人類的話，推薦大
家可以去翻翻李開復寫的人工智慧來了，而如果是想了解到底深度學習是怎樣架構的話推薦
大家去找李弘毅教授的影片來看看，那對機器學習中的數學有興趣的話則是可以看田神在coursera上開的課程。

<br>

## 參考資料
- <人工智慧來了> 李開復
- 李弘毅教授講義
- GAN-ZOO
- IDC: The premier global market intelligence firm

