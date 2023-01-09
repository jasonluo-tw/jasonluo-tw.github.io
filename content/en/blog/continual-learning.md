---
author: "Jason Luo"
title: "Continual learning"
description: "The summary of continual learning in a webinar"
tags: ["DL", "tech"]
date: 2022-01-23
math: true
thumbnail: https://images.unsplash.com/photo-1454165804606-c3d57bc86b40?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1170&q=80
---
## 前言
&emsp;幾個月前聽了一場有關深度學習的線上演講，那次的演講內容是有關於continual learning，此種學習方法的出現是為了解決現在深度學習模型的一個致命弱點，並提出一些改善的方法，由於此子領域仍算是DL裡非常新的研究課題，所以還有很多值得研究的地方。在這裡我紀錄了一些演講的重點。

<!--more-->
## DL的金魚腦
&emsp;在現今的深度學習中，研究人員發現深度學習模型是有一個非常健忘的金魚腦。當我們訓練了一個模型分辨小貓和小狗之後，接著讓此模型再學習分辨飛機和火車，雖然訓練完的模型學會了如何分辨飛機和火車，但是它會完全忘記如何分辨貓狗，從而導致在辨別貓狗的任務上有非常差的表現。科學家們將此現象稱作<span style="color: red">Catastrophic forgetting</span>(災難性或毀滅性的遺忘)，名稱聽起來非常嚴重，但確確實實地指出了現今深度學習非常嚴重的缺陷。而Continual learning(Incremental learning)就是為了解決這問題而出現的。

## 為什麼DL會忘記
&emsp;但到底為何會出現Catastrophic forgetting呢？其實從loss function的方向去思考很快就會知道答案了。在不同的任務之下，loss function其實長得很不一樣，當我們在訓練第二個任務的時候，模型是往當下的loss少的方向走，但有可能對於前一項任務來說卻往往是loss變高的地方走，因而造成學會了當下的任務而遺忘之前的任務。
<center><img src="https://i.imgur.com/EHos8XZ.png" style="width:600px"></center>
From https://arxiv.org/pdf/2104.07286v1.pdf

## Continual learning的規則及方法
現在科學家們列了幾條規則，要符合以下的規定才算是Continual learning
1. Learn one task after the other
2. Without storing(many) data from previous tasks
3. Without memory footprint growing(significantly) over time
4. Without (completely) forgetting old tasks

而學者們主要提出的方法可以分為以下幾種：
- Regularization based: **Lwf, EBLL, EWC, SI, MAS, IMM, ...**
    - Data-focused regularization
    - Model-focused regularization
- Rehearsal/Replay: **iCaRL, DGR, GEM, ...**
- Architecture-based: **PackNet, progressive nets, HAT, ...**

## Regularization-based
&emsp;在Regularization-based中，主要的方式就是在loss function中增加regularization term去限制模型的weights或是在學習新任務時，限制預測前一個任務的結果(output)不要因為學習新任務而有太大的差距。所以可以分為data-focused和model-focused的regularization，前者是針對模型的output去做限制，而後者則是針對模型的weight去做限制。

### 1. Data-focused regularization
&emsp;此方法是當新任務的input經過Feature Extraction後，除了通過新任務的classification layer之外，同時也通過舊的(上一個任務)classification layer，得到一個output，將此output與舊model(update前一個)的output算一個loss(e.g. l2 loss)，以這樣的方式可以讓model在學習新的任務的同時，也仍能保持在舊任務的表現。因為需要有舊model的output，所以在訓練階段的時候會有兩個model，一個是舊的，一個是更新參數之後的model。
<br>
<img src="https://i.imgur.com/SPG0FXT.png" style="width:600px">

<br>

**pros: Simple method; good results for related tasks**
**cons: Poor results for unrelated tasks**
**Relevant paper:[Li & Hoiem 2016](https://arxiv.org/pdf/1606.09282.pdf)**



### 2. Model-focused regularization
&emsp;此方法是針對模型的weight去做regularization，在更新參數的同時，要確保參數一下子不會變動太多，假如一次更新變動太多的話，會使得model容易忘記舊任務(亦即weight會往新任務loss低的地方走，但並不會考慮舊任務的loss)，所以在loss function的第二項中，我們加入了一個regularization term，防止參數一下變化太多。同時我們並不會要求模型中每個weight的regularization一樣，因為不同參數對於不同任務的重要性不同，因此用一個$\lambda_k$來決定各個參數的重要性。

<center><img src="https://i.imgur.com/5zJZ7A3.png" style="width:500px"></center>

<br>

&emsp;然而，如何計算\\(\lambda_k\\)呢？講者提出了兩個paper，第一個是Elastic weight consolidation(EWC)。EWC計算fisher information matrix，並考慮所有前面的task去計算出\\(\lambda_k\\)。利用EWC的方法可以找到在兩個任務中都還不錯的解(下圖)，在這邊並不會提到細節，如想知道詳細的過程的話就請參考paper囉。
<br>
<center><img src="https://i.imgur.com/N16rpbx.png" style="width:400px"></center>
<br>

**pros: Agnostic to architecture; Good results empirically**
**cons: Only valid locally; Need to store importance weights**

&emsp;而另一個方法則是Memory aware synapses。此方法只須使用前一個task的weight來計算\\(\lambda_k\\)，並使用gradient的改變量來計算\\(\lambda_k\\)

**pros: Agnostic to architecture; Leverages data & output**
**cons: Only valid locally**

### 比較:
- EWC
    - Indiv. penalty for each previous task \\(\sum_k\sum_{i<n}\lambda_{k}^{n-i}(\theta_k^n-\theta_k^{n-1})^2\\)
    - Fisher information matrix for \\(\lambda_k\\)
- Memory aware synapses
    - Considers only the previous task \\(\sum_k{\lambda_k(\theta_k^n-\theta_k^{n-1})^2}\\)
    - Change in gradients for \\(\lambda_k\\)


### 相關的paper:
1. Elastic weight consolidation [Kirkpatrick et al., 2017]
2. Memory aware synapses [Aljundi et al., 2018]
3. Path integral/Synaptic Intelligence [Zenke et al., 2017]
4. Moment matching [Lee et al., 2017]
5. Pathnet [Fernando et al., 2017]

## Rehearsal/Replay
&emsp;此種方法是紀錄前幾個任務的一些samples或是利用generative model去產生之前任務的samples，藉由這種紀錄或產生sample的方式，在訓練當前任務的時候，可以同時把之前任務的samples一起做訓練，以達到增量學習的效果。然而，要記錄多少的samples、如何挑選samples以及如何使用他們來達到最大的效果，就是此種方法要探討的問題。

### 1. iCaRL [Rebuffi et al. 2017]
1. 挑選靠近每個class平均的位置的samples
1. Selects samples that are closest to the feature mean of each class
2. Use knowledge distillation loss
   [Distilling the Knowledge in a Neural Network](https://arxiv.org/abs/1503.02531)
3. The problem is split into:
    1. learning features
    2. using NCM(like nearest neighbor) classifier
    
**pros: Clever use of available memory**
**cons: 
    1. potential issues with storing data, e.g., privacy
    2. Limited by the memory capacity(the more the better)**

### 2. Deep Generative Replay
&emsp;此種方法是利用generator來紀錄前面任務的訊息，由generator負責產生input data，而另一個模型則負責做預測或分類，為solver(classifier)。由generator和solver組成一個**Scholar**，此Scholar會在每一輪的task中更新。

<img src="https://i.imgur.com/B1V6RNP.png" style="width:200px">

#### 大致上的訓練流程:
- 在task t的時候，我們會訓練一個新的Scholar
    - 對於generator的訓練資料
        - 當前任務下(task t)的data
        - 前一個任務(task t-1)中generator產生出來的data
        <img src="https://i.imgur.com/Bvmi3S9.png" style="width:500px">

    - 對於solver的訓練資料
        - 當前任務下(task t)的pair data
        - 前一個任務(task t-1)中generator產生出來的data以及通過Solver後的data
        <img src="https://i.imgur.com/s6jWTyp.png" style="width:500px">

**pros: avoids memory issues**
**cons: 
    1. accumulation of errors
    2. no control over the class of the generated samples**

### 相關的paper:
1. iCaRL [Rebuffi et al. 2017](https://arxiv.org/pdf/1611.07725.pdf)
2. Deep Generative Replay [Shin et al. 2017](https://arxiv.org/pdf/1705.08690.pdf)

## Architectured-based
### PackNet [Mallya & Lazebnik 2017](https://openaccess.thecvf.com/content_cvpr_2018/papers/Mallya_PackNet_Adding_Multiple_CVPR_2018_paper.pdf)
此方法是利用weight pruning的概念來達到continual learning(incremental learning)。如下圖所示，訓練完某個model之後，利用pruning的方法把模型修小，剩下的weights就可以拿來訓練下一個任務，訓練完之後再繼續pruning，一直反覆此操作來學習不同的任務。此方法雖然可以達到很高的準確度，但必須事前知道有多少任務學習，才能分配好各個任務所需要的weights，不然學習到後面，後面的任務可能就沒剩下多少的weights可供訓練。

<img src="https://i.imgur.com/5rEQOr5.png">

**pros: fixed memory consumption, avoids forgetting**
**cons: needs the total number of tasks**


## A comparative analysis
最後提供幾個可用於continal learning(incremental learning)的dataset以及各種方法的比較結果，如有興趣想知道更詳細的分析可以參考Lange et al.2020的[A continual learning survey](https://arxiv.org/pdf/1909.08383.pdf)這篇結果。

### dataset:
- TinyImagenet: small, balanced, class-incremental
- iNaturalist: large-scale, unbalanced, task-incremental

<img src="https://i.imgur.com/2s3fhC0.png">

### 各種方法的比較結果(TinyImagenet)
#### Regularization & Architecture based
<img src="https://i.imgur.com/mfv20Do.png" style="width:1000px">

#### Rehearsal & Replay based
<img src="https://i.imgur.com/EpjcOfM.png" style="width:1000px">

### Takeaways
- Rehearsal/replay based methods only pay off when storing significant amount of exemplars
- PackNet results in no-forgetting and produces top results
- MAS more robust than EWC


## Sources
- [Video course](https://www.youtube.com/watch?v=A7DDk2euG2A)
- [Li & Hoiem 2016 - data regularization](https://arxiv.org/pdf/1606.09282.pdf)
- [Elastic weight consolidation](https://arxiv.org/pdf/1612.00796.pdf)
- [Memory aware synapses](https://arxiv.org/pdf/1711.09601.pdf)
- [iCaRL](https://arxiv.org/pdf/1611.07725.pdf)
- [Deep Generative Replay](https://arxiv.org/pdf/1705.08690.pdf)
- [PackNet](https://openaccess.thecvf.com/content_cvpr_2018/papers/Mallya_PackNet_Adding_Multiple_CVPR_2018_paper.pdf)
- [A continual learning survey](https://arxiv.org/pdf/1909.08383.pdf)
