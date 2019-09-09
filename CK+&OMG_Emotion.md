## [为CK+人脸情绪识别数据集所做的数据库设计](https://www.cnblogs.com/cadenza/p/9982005.html)

### 需要了解的术语

#### FACS

Facial Action Coding System 面部行为编码系统

不同的脸部肌肉动作和不同的表情对应的关系

将人脸划分为若干既相互独立又相互联系的运动单元（AU）



### CK+数据集

与CK数据集相比

- 序列的数量增加了22%，受试者的数量增加了27%。
- 每个序列的目标表达式都是完全FACS编码的
- 情感标签已经被修改和验证。
- 添加了几种类型的微笑及其相关元数据的非定态序列。
- 包括表情的label和AU的label

#### 结构

123个subjects，593个image sequence，每个image sequence的最后一张frame都有AU的label，而593个image sequence中有327个sequence有emotion的label

四个文件夹(当前考虑1和4)

- the images

  Cohn-kanade-images.zip

  共有593个序列跨越123个受试者，这些序列在峰值帧上编码为FACS。所有的序列都是从中性脸到峰值表情。(从平静到表情表现峰值的图片，实际使用中建议使用比较明显的图片，并进行相应的预处理。)

- the landmarks

  所有序列都用AAM跟踪，每个图像有68个地标。

- The FACS code files

  对于每个序列(593)只有一个FACS文件，这是最后一帧(峰值帧)。文件的每一行对应一个特定的AU，然后是强度。

- the emotion coded file

  593个序列中只有327个序列具有情感序列。这是因为它们是唯一符合原型定义的。与FACS文件一样，每个序列只有一个情感文件，即最后一帧(峰值帧)。应该只有一个条目，其范围为0-7(即0=中性，1=愤怒，2=蔑视，3=厌恶，4=恐惧，5=高兴，6=悲伤，7=惊讶)。N。只有327个文件——如果没有文件，那就意味着没有情感标签(不好意思说得太明确，但这样可以避免混淆)。

  0=neutral, 1=anger, 2=contempt, 3=disgust, 4=fear, 5=happy, 6=sadness, 7=surprise

### 该网站做的工作

- 将文件夹1和文件夹4作为两个表

  Groups - 每个元组对应一个主体所做的一个表情序列的总和(suject\group\emotion\num-组内图片总数)

  imgs - 每个元组对应一个张图片（subject\group\no\addr)

- 编写脚本将图片倒入数据库中，可方便获取一张图片的地址以及其情绪类型

## OMG-Emotion

处理受限制的情绪或者简单的即时情绪

大部分研究仍然给予瞬时表情分类——用不同的形式来描述单个情绪表达

https://github.com/knowledgetechnologyuhh/OMGEmotionChallenge

### dataset

OMG-Emotion Dataset由420个相对较长的情绪视频组成，平均时长为1分钟，收集自Youtube多个频道。这些视频是根据与独白相关的特定搜索词自动选择的。使用独白视频可以**让不同的情感行为在一个随着时间推移而逐渐改变的语境中呈现出来**。视频根据话语分成多个片段，每个话语至少由五个独立的受试者使用Amazon Mechanical Turk工具进行注释。为了维护每个视频的上下文信息，每个注释者按顺序观看视频片段，并必须使用唤醒/效价量表对每个视频进行注释。

在注释数据集时，还向每个注释器提供了该视频的完整上下文信息。这意味着每个注释器不仅可以考虑视觉和音频信息，还可以考虑每个视频的上下文，即通过注释工具提供的上下文剪辑在当前和以前的话语中说了什么。这样，每个注释都基于多模态信息，不同于最近的情感识别数据集。这使得我们的语料库在跨模态研究中具有优势，尤其是在分析音频、视觉和语言模态时。

### structure

- omg_TrainVideos.csv
- omg_ValidationVideos.csv
- DetailedAnnotations 就像标签
- omg_TrainTranscripts.csv
- omg_TestTranscripts.csv

每个data csv文件包括

- link youtube该视频的链接
- start 视频的开始时间
- end 视频的结束时间
- video video的id，用来连接对应的annotations
- utterance 名字，用于连接对应的annotations
- arousal arousal的黄健标准
- valence valence的黄金标准
- emotionMaxVote 对所有注释进行投票产生的分类情绪

每个transcripts csv包含

- link youtube视频的链接
- video video的id，用于和annotations进行连接
- utterance ditto
- tanscript 每个视频的文字记录

DetailedAnnotations文件夹包含每个语句的所有注释。每个注释都是在考虑将视频作为序列的一部分时做出的，因此在设计模型时使用这些附加信息可能很有用。

转录本文件夹包含数据集中每个话语的所有转录本。

### label

- 0 - Anger
- 1 - Disgust
- 2 - Fear
- 3 - Happy
- 4 - Neutral
- 5 - Sad
- 6 - Surprise