# 深度学习与人类语言处理

> 本门课Text内容和Speech内容 = 5：5

语音的难点

1. 只有56%的语言有文字，并且存在文字的但是很少流通，因此语音的数据域大于文本的数据域
2. 人类语言十分复杂，我们使用KHz表示采样率，16Khz表示1s含有16k个采样点。
3. 没有一个人说同一段话两次，每一次说出的声音讯号是不同的

文字的复杂

1. 句子的长度是变长的，可能由过多个词构成，但是单纯讨论最长的句子是没有意义的。可以不断扩展。



根据输入输出我们可以将模型分成以下类

- 音频 -> model -> 文本：Speech Recognition（ASR）
  - 传统的语音识别模型很复杂，由多个组件构成，包括语言模型和文本模型。
  - 深度学习模型使用的是一个端到端的模型完成。
- 文本 -> model -> 音频：Text to Speech Synthesis（语音的生成TTS）
  - 多数时间是好的，在真实的系统上会存在少量这个问题（破音、缺音
- 音频 -> model -> 音频：
  - Speech Separation
    - cocktail party effect（鸡尾酒会效应）
  - Voice Conversion
    - 输入人声音的树和输出人声音数据的构建
    - Unsupervised Voice Conversion（one shot learning）
- 文本 -> model -> 文本
  - Translation
  - Summarization
  - Chat-bot
  - Quenstion Answering（重点关注）
- 音频 -> model -> 类别
  - Speaker recognition
    - 检测是谁说的这句话
  - Keyword Spotting
    - 检测输入的语音是否包含keyword
- 文本 -> model -> 类别



![image-20230319212853141](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303192129979.png)

语言的预训练模型越来越大

- Text Generation
  - Autoregressive：按照从左到右的顺序生成
  - Non-autoregressive：不按照顺序生成，是按照重要性和词之间的联系生成
  - ![image-20230319213200632](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303192132807.png)



- Meta learning = Learn to learn 模型先在很多任务上学习，然后借鉴经验在新的任务上进行学习
- Learning from Unpaired Data
- Knowledge Graph
- Adversial Attack
  - Speech
    - Anti-spoofing system
    - Speech recognition is easy to fool
  - NLP