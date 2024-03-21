---
date: 2024-03-21 09:00:00
tags:
  - prompt
  - ai
title: Codesignal提示工程教程
---


课程地址： https://learn.codesignal.com/preview/course-paths/16/prompt-engineering-for-everyone

![image.png](https://res.cloudinary.com/leo2rosym/image/upload/v1711002684/2024/03/24-3-21-1_rheixx.webp)

一致性格式化的重要性
对提示进行一致的格式化不仅仅是为了让它们看起来整洁；这意味着要向人工智能清晰地传达你的意图。想象一下，如果你在给别人烘焙蛋糕的指导，但没有按顺序列出步骤，而是将它们全部混在一起。结果会怎样？混乱，很可能，蛋糕也不会很好吃。同样的原理也适用于大型语言模型（LLMs）。通过以清晰、结构化的方式呈现你的提示，你极大地增加了获得期望输出的机会。

尽管有很多方法可以结构化你的提示，但在这个课程中，我们将教你由CodeSignal的提示工程师和人工智能专家开发的Markdown提示框架（MPF）。

MPF是一种创建高度可读、易于维护且有效的提示的非常有效的方法，并且是Cosmo许多方面的核心。
## **Markdown提示框架（MPF）**

- ASK - 我们请求大语言模型做什么？
- CONTEXT - 大语言模型需要了解什么信息，才能准确地回应？
- CONSTRAINTS - 回应时需要遵循哪些限制？
- EXAMPLE - 你满意的输出样例是什么？


---

请写一首关于一只名叫Cosmo的聪明太空柯基犬的短诗，并确保这首诗是用俳句风格写成的。

```
shell
ASK
创建一首关于一只名叫Cosmo的聪明太空柯基犬的俳句诗。

CONTEXT
- 主题是一只名叫Cosmo的太空柯基犬。
- 这只柯基犬被描述为聪明。
- 诗歌应遵循传统的俳句结构：由三行组成，第一行5个音节，第二行7个音节，第三行5个音节。

CONSTRAINTS
- 诗歌必须简洁，遵守俳句的17个音节规则。
- 它应该捕捉到Cosmo的智慧本质以及他的太空冒险。
- 诗歌应该唤起与太空相关的意象和柯基犬的独特品质。

EXAMPLE
智慧之星，
Cosmo穿越浩瀚宇宙， 
眼中充满智慧。
```

---

我正在写一本关于一只太空柯基犬的书，它穿越宇宙帮助每个人学习新技能。我正在写到柯基犬降落到地球上，那里的每个人都想学习关于提示工程的知识。写一个适合儿童的笑话，关于这只柯基犬（名字是Cosmo），他和一颗彗星搞混了，成为笑话的结尾。

```
shell
ASK
创造一个涉及名叫Cosmo的太空柯基犬的、适合儿童的笑话，这只柯基犬以帮助生物学习新技能而闻名，笑话的结尾是一个与彗星搞混的情节。

CONTEXT
- 主角是一只名叫Cosmo的太空柯基犬。
- Cosmo的使命是穿越宇宙教授技能。
- 背景设定在地球上，那里的每个人都对学习关于提示工程感兴趣。
- 笑话应该是轻松愉快的，适合儿童。

CONSTRAINTS
- 保持笑话简短简单，以便儿童容易理解。
- 确保幽默是无辜的，不包含任何不当内容。
- 笑话的结尾应该利用“Cosmo”和“comet”之间的相似性。

EXAMPLE
为什么地球上的每个人都认为太空柯基犬Cosmo是一颗彗星？
因为每次他教授一个新技能，他真的知道如何“star-t”一个趋势！

[注：笑话利用了“star”作为天体的词和“start”作为开始某事的词的双关语。]
```

---

我正在写一个关于一只独特的太空柯基犬的激动人心的故事，这只柯基犬穿越宇宙帮助奇怪的生物学习新技能。这只太空柯基犬的名字是Cosmo，它刚刚降落在一个遥远的星球上，那里的所有外星人都渴望了解提示工程的微妙之处。写一个五行的、适合儿童的诗歌，关于Cosmo，最后有一个关于彗星的有趣转折。例如，它可以是这样的，但不要完全一样。

Cosmo the space corgi, so brave and so bright,
Traveled the stars from morning till night.
Teaching aliens about prompts, oh what a sight,
Until a comet passed by, with a tail so tight.
Cosmo barked, "Chase it?" and took off in flight!

```
shell
ASK
撰写一首五行的、适合儿童的诗歌，关于一只名叫Cosmo的太空柯基犬，他帮助各种生物学习新技能，最后有一个关于彗星的幽默转折。

CONTEXT
- 主人公是一只名叫Cosmo的太空柯基犬。
- Cosmo的使命是帮助宇宙中的生物获得新技能。
- 当前背景涉及一个外星人渴望学习提示工程的星球。
- 诗歌应该引人入胜，轻松愉快，适合儿童。
- 转折应该是有趣的，创造一个与彗星相关的令人愉快的惊喜。

CONSTRAINTS
- 诗歌必须由五行组成。
- 最后一行应该引入一个关于彗星的滑稽误会或混淆。
- 诗歌应保持一致的节奏和韵律，以便于阅读。
- 确保幽默是温和的，适合年轻观众。

EXAMPLE
在宇宙中，Cosmo翱翔，
向外星人传授提示，多么壮观。
在一个星球上，他分享他的知识，
当一颗彗星出现，会发生什么？
“去追！”Cosmo想，毫不犹豫地追逐！
```

---

我需要一首关于团队精神的短暂而振奋人心的歌曲，用于一部儿童动画足球节目。节目的名字叫“梦之队”，主要角色是一群组成足球队的动画动物。队伍包括守门员Penny the Penguin（企鹅），前锋Leroy the Leopard（豹子），以及队长兼中场Bobby the Bear（熊）。歌曲必须提到这些角色，并强调团队合作的重要性。歌曲应该有一个朗朗上口的副歌和两段歌词，就像给出的例子一样：

(Chorus)
Penny, Leroy and Bobby too,
Playing football, dream come true!
Through wind or rain, they got the knack,
With teamwork, there's no turning back.

(Verse 1)
A penguin in goal, wings spread wide,
Leroy leaps, no place to hide.
Bobby calls 'pass', then 'shoot',
When they play together, it's a hoot!

(Verse 2)
Through each challenge, they found a way,
United under the sun's bright ray.
Their dream was football, their spirit strong,
Together in team, where they belong.

```
shell
ASK
为一部名为“梦之队”的儿童动画足球节目创作一首短暂而振奋人心的歌曲，庆祝团队精神。歌曲应该提到主要角色——守门员Penny the Penguin（企鹅），前锋Leroy the Leopard（豹子），以及队长兼中场Bobby the Bear（熊）——并强调共同努力的重要性。它应该有一个朗朗上口的副歌和两段歌词。

CONTEXT
- 背景设定是一部动画儿童足球节目。
- 主要角色是组成足球队的动画动物。
- 队伍包括Penny the Penguin，Leroy the Leopard，和Bobby the Bear。
- 歌曲应该传达关于团队合作和协作的积极信息。
- 歌曲应该引人入胜，适合儿童，并有一个难忘的旋律。

CONSTRAINTS
- 歌曲应该由一个副歌和两段歌词组成。
- 歌曲中必须提到每个角色的名字。
- 歌词应该简单明了，便于儿童跟唱。
- 歌曲应该有一个积极和振奋人心的基调。

EXAMPLE
(Chorus)
我们是梦之队，强大而宏伟，
一起玩耍，梦想成真。
有了Penny，Leroy和Bobby，我们团结一心，
在团队合作中，我们飞向光明的天空！
```

---
