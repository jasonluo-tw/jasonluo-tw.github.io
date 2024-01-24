---
author: "Jason Luo"
title: "使用深度學習來預測極短期降雨"
description: "Deep Learning on Precipitation Nowcasting"
tags: ["DL", "weather", "tech"]
date: 2024-01-24
thumbnail: https://images.unsplash.com/photo-1519692933481-e162a57d6721?q=80&w=2070&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D
---
&emsp;自2012以來，由於深度學習在影像辨識上的巨大成功，人們開始將深度學習應用在許多的領域上，從電腦視覺、自然語言處理、語音辨識、推薦系統、蛋白質結構預測等，各個領域都可以看到深度學習(AI)的蹤跡。不過，我們卻很少聽到AI與氣象的結合，這次我們就來講AI在極短期降雨預報上的應用。

<!--more-->

## 傳統光流法
&emsp;天氣預報可以大致上分為-極短期、短期、中期、長期，而在這幾個類別中，極短期預報是數一數二困難的。所謂的極短期預報通常就是預報接下來1~3個小時是否會下雨，要在這麼短的時間內讓複雜的數值模擬(Numerical Weather Prediction)跑出預測其實是很耗費運算資源的，所以通常我們會應用別的技術在極短期預報上。最常見的其中一個方法就是使用雷達回波並搭配電腦視覺演算法中的光流法(optical flow)。
<center>
<img src="https://i.imgur.com/TggdNMT.png"></img>
</center>

&emsp;光流法，就是利用照片中光影明暗的變化來推斷物體移動的速度，利用兩張連續(時間間隔短)的照片或圖片，再使用光流方程式及一個假設(兩個方程式但有三個未知數，所以用了一個假設來確定解)來求解出照片中的物體及方向。而在天氣的領域中，我們可以利用[雷達的觀測](https://www.cwa.gov.tw/V8/C/W/OBS_Radar.html)來測到空中的水滴，我們就可以利用連續兩張的雷達回波圖結合光流法來推估現在降雨的移動情形。

***如下圖，利用兩張連續的雷達回波，分別是18:50、19:00，就可以推斷出當下19:00降雨的移動情形，之後再利用平流(外延的方式)預報未來短時的降雨。***

<center>
<img src="https://i.imgur.com/uGiKIEO.png" style="width:45%; display:inline-block">
<img src="https://i.imgur.com/sntvWS0.png" style="width:45%; display:inline-block">
</center>

利用上述兩張雷達回波，藉由**光流法**計算出下圖的移動向量(motion vector)
<center><img src="https://i.imgur.com/Oqz1t1e.png" style="width:60%"></center>

有了motion vector之後，我們就可以將雷達回波(可以想成是觀測到的雨滴)進行外延，而得到未來降雨的走向。
<center><img src="https://i.imgur.com/AvVWWoz.gif" style="width:60%"></center>

## 傳統光流法的限制
&emsp;雖然使用光流法做出的預報在非常短的時間之內有不錯的預報結果，算出來的評價分數(Threat Score)也蠻高的，但實際上在應用時仍會遇到許多的問題。像是預報出來的降雨不真實，實際看預報時會發現降雨好像只有平移而沒有增強或減弱，因為光流法只有推估出降雨的速度，但強度隨時間的變化卻無法推估出來，因此導致在預報短時間增強或減弱的降雨時(e.g.夏季的午後雷雨)，會報的很差。
&emsp;除了上述原因之外，利用光流法推估出的速度也可能不夠精確。在利用光流法的時候，其實假設了物體本身的亮度不變且兩張圖片的時間差是很小的。而在實際的情境中，雷達回波的強弱和形狀大小都有可能在短時間內有變化，而且實際作業上兩張連續的雷達回波時間差並非很小，所以以上兩點會影響到推估出的降雨速度。
&emsp;再者，光流法只能推估出當下的速度，但**不能推估出速度隨時間的變化**，所以我們其實假設了在短時間內(1小時)速度隨時間的變化是微小可忽略不計的。而這點雖然在短時間內的影響可能不大，但如果要把預報時間拉長時(~3小時)，就不能不考慮這點。所以有些[研究工作1](https://ieeexplore.ieee.org/document/9883722/)、[研究工作2](https://www.sciencedirect.com/science/article/abs/pii/S0022169419308753)試著結合物理方程(Navier-Stokes equation)來獲得雷達回波移動速度隨時間的變化，以這種方式來拉長預報的時間及準確性。

最後，總結一下上述提到使用光流法會造成的一些限制與缺點：
- 降雨強度不會隨時間改變
- 因為光流法一開始的假設和限制，推估出來的motion vector不夠精確
- 利用光流法無法推估出速度在未來的變化

## 深度學習的應用
&emsp;因為光流法本身的缺點，加上近幾年深度學習在各個領域大放異彩，所以有不少研究者開始嘗試使用深度學習在極短期降雨預報上，期望能帶來不錯的結果。

### ConvLSTM & RainNet(UNet) 
&emsp;2015年，香港科技大學及香港天文台共同發表了一篇[paper](https://arxiv.org/pdf/1506.04214.pdf)，他們提出了**ConvLSTM**，把原本處理影像的Convolution layer加上處理時間序列的LSTM合在一起，這樣就可以用神經網路處理同時擁有影像和時序的回波圖。此模型包含了Encoding Network和Forecasting Network，先將前面幾個time step的回波圖放到Encoding Network裡產生hidden state tensors，然後Forecasting Network的前面幾層是複製Encoding Network的後面幾層，再把Forecasting Network的output結合起來通過1x1 Conv得到最後的預測。

<center><img src="https://i.imgur.com/WCC7KEW.png"></center>

&emsp;除了ConvLSTM，2020年發表的[RainNet](https://gmd.copernicus.org/articles/13/2631/2020/)是使用鼎鼎大名的UNet模型架構來處理極短期預報，並在CSI(Critical Success Index)、MAE取得比光流法(Rainymotion)還要好的結果。
<center><img src="https://i.imgur.com/62fltjE.png" style="width: 80%"></center>

上圖為模型架構，input為前4個time step的回波圖，這裡的time interval為5分鐘，所以input為前20分鐘內的雷達回波，然後output則是下一個time step(5分鐘後)的預測。如果要預測更長的time step的話，則使用recursive的方式，亦即將現在的預測結果當成input繼續預測下一個時間點的降雨(此種方法即為AutoRegressive Model)。

<center><img src="https://i.imgur.com/QWpU5ZF.png" style="width:80%"></center>

此圖是RainNet跟光流法(Rainymotion)和Persistence方法的比較。可以發現在MAE、CSI threshold小於5的時候RainNet有比較好的表現。但在中、大雨的時候(threshold:10,15)的時候則會發現表現變差。

除了CSI和MAE的表現上，作者也比較了一下RainNet和Rainymotion預測出來的回波圖之間的差異。並計算了PSD(power spectral density)來表示預測跟觀測比的空間平滑程度。從下圖可以看到RainNet做出的預測比觀測平滑不少，而相較之下Rainymotion的預測就沒有那麼平滑。從PSD上也可以很明顯地看到Rainymotion的藍線跟觀測的黑線相近，但RainNet的紅線在波長較短的地方(頻率較高)就跟黑線差距很大，這也明顯代表使用深度學習時，很容易產生**模糊的預測結果。**

<center><img src="https://i.imgur.com/MiqHqao.jpg" style="width:80%"></center>

### DGMR
&emsp;不管是Unet或是ConvLSTM等不同的模型架構皆會產生**模糊的預測結果**。為了改善這個現象，DeepMind在2021年的論文嘗試使用conditional GAN來訓練模型並取得不錯的成果。他們的模型架構相比上述兩者要來得複雜許多。整個模型可以分成generator, spatial discriminator跟temporal discriminator，下圖為整個模型的架構圖。

<center><img src="https://i.imgur.com/kTnZ9uz.png" style="width: 80%"></center>

&emsp;這個模型有一個令人值得關注的地方是它可以做probabilistic forecast。因為input是random的normal distribution(上圖中的Latent Conditioning Stack)，所以使用相同的雷達回波當輸入，可以sample出不一樣的預測結果，可以做到probabilistic nowcasting。

&emsp;以下是幾個模型的比較，包括了筆者自己訓練的DGMR、沒有使用GAN training的模型(Seq2Seq)以及經過一些調整的開源專案(Rainymotion)。
從比較中可以看出Seq2Seq(沒有使用GAN訓練方式)的模型會產生出模糊的預測，而DGMR有使用GAN的方式做訓練，可以看出預測結果會比較真實。

<center>
<iframe width="560" height="315" src="https://www.youtube.com/embed/QveEN31gkRY" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
</center>

## 小結語
&emsp;這幾年來，研究人員及科學家漸漸嘗試使用AI來做氣象相關的應用。不管是極短期的降雨預報(DGMR, RainNet)、全球預報(GraphCast, PanguWeather)、風速降尺度(WindTopo)或是應用可解釋性模型(XAI)在氣象數據上，大家漸漸意識到或許AI可以成為其中一種方法來處理氣象上的問題或做預報，同時也提供了另一個面向和角度來看氣象的問題。現在AI在氣象上的應用還在起步的階段，還有很多待解決的問題，但如果能好好的結合AI與氣象相關的知識，或許在未來能夠有不錯的成果出來，期待那一天的來臨！

## Refernece
1. [Rainymotion](https://github.com/hydrogo/rainymotion)
2. [PySteps](https://pysteps.github.io/)
3. [RainNet](https://gmd.copernicus.org/articles/13/2631/2020/)
4. [Convolutional LSTM Network: A Machine Learning Approach for Precipitation Nowcasting](https://arxiv.org/pdf/1506.04214.pdf)
5. [Improved rainfall nowcasting using Burgers’ equation](https://www.sciencedirect.com/science/article/abs/pii/S0022169419308753)
6. [Integrated Physics-Based Nowcasting Model for Precipitation Radar Image Sequence](https://ieeexplore.ieee.org/document/9883722)
7. [A review of radar-based nowcasting of precipitation and applicable machine learning techniques](https://arxiv.org/abs/2005.04988)
8. [Skilful precipitation nowcasting using deep generative models of radar](https://www.nature.com/articles/s41586-021-03854-z)

