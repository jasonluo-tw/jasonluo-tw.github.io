---
author: "Jason Luo"
title: "隨筆 - 現今AI模型的限制"
description: "Some limitation of AI(LLM) nowadays"
tags: ["DL", "LLM", "tech"]
date: 2023-07-23
thumbnail: https://images.unsplash.com/photo-1518749031467-bb37f48aee10?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=2070&q=80
---
&emsp;繼上次在Threads介紹現今的AI已可以做到理解各種資料的輸入(文字、圖像、語音...)，並且能夠給予很流暢的回覆，幾乎沒什麼文法錯誤、句子也通順。除此之外，幾個月前微軟發表了一篇論文([Sparks of Artificial General Intelligence: Early experiments with GPT-4](https://arxiv.org/abs/2303.12712))，聲稱GPT4已經具備AGI的能力。
<br>
&emsp;雖然自從chatGPT推出之後讓大家為之震驚，但其無所不能的外表之下卻還有很多的問題等待被解決。
<!--more-->

- 大型LLM的幻覺(Hallucination)：這也就是俗稱的一本正經講幹話，有跟chatGPT聊過天的人應該多少都有體會到這個。AI三巨頭之一Yann LeCun認為現今的Auto-Regressive LLM容易導致幻覺的產生，應用其他方法解決([PPT](https://reurl.cc/y7424M))。
- 邏輯推理能力：只要問GPT4數理問題，會發現有時候它會在一些簡單的地方算錯，AI的邏輯能力似乎還有待加強。現在大家會使用一種叫CoT(chain of thought)的方式來增加AI的答對率，但本質上要如何提高AI每一步的推理、邏輯能力卻還需要更多的研究。openai前陣子提出的論文([Let's Verify Step by Step](https://arxiv.org/abs/2305.20050))，就是想透過**process supervision**的方式改進AI的推理能力。
- 學習效率：現今的AI雖然很厲害，但其實要做出這樣厲害的AI需要大量的訓練資料+非常大的模型，並且要反覆讓AI看很多遍訓練資料，這也代表需要花很多時間和金錢才能讓模型講人話。反觀，人類的學習效率之高，例如很會舉一反三、因果推論也非常強，但卻只需要些許能量(吃飯)來維持腦部運作。所以如何提高AI學習效率在未來是一件很重要的事。
- AI模型大小：現今的AI模型必須很大才能擁有超強的能力([Emergent Abilities of Large Language Models](https://arxiv.org/pdf/2206.07682.pdf))，而這樣巨大的模型卻很難放到裝置上或edge device上，如何縮小模型大小卻保持同樣的能力是未來很重要的研究方向之一。

&emsp;除了以上提到的之外，其他問題還有像災難性遺忘(catastrophic forgetting)、資料隱私、Adversarial attack等...

&emsp;雖然chatGPT的降臨看似很多NLP的問題都被解決了，但其實還是有很多東西待解決，似乎離AI要毀滅世界的路還有一段要走？

