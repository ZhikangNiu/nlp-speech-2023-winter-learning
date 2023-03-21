# [DLHLP 2020] Speech Recognition (2/7) - Listen, Attend, Spell

>  W. Chan, N. Jaitly, Q. Le, and O. Vinyals, “Listen, attend and spell: A neural network for large vocabulary conversational speech recognition,” in ICASSP 2016.

## Listen，Attend，and Spell（LAS）

1. Listen -> Encoder，Spell -> Decoder. 

2. 是一种典型的注意力的seq2seq

3. LAS的整体架构

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20191216220112519.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTUyOTQxMw==,size_16,color_FFFFFF,t_70)

   工作流程：

   - 将语音信号特征输入到双向的RNN中（encode，即就是Listen）
   - 做Attention。在不同的时刻关注输入的不同部分（decoder）
   - 根据Attention的结果，使用Beam search或greedy search进行decode

4. Listen

   ![image-20230320143353291](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201434463.png)

   我们希望encoder完成以下的目标

   - 提取内容信息
   - 去除一些语音中的噪声信号、说话人的偏差

   Encoder的具体结构

   - 1D CNN：考虑到周围的向量，也可以不断叠加，扩大感受野，更高层的Filter可以考虑更多的长序列
   - 单向的RNN
   - 双向的RNN
   - CNN+RNN
   - self attention layer

   Listen会对语音做downsampling

   ![image-20230320143819900](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201438976.png)

   ![image-20230320144005779](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201440870.png)

​		Truncated Self-attention是在一定范围里面做attention

4. LAS中的Attention

   选择一个$z^0$和经过encoder的高级表达$h$通过match函数进行计算，输出一个标量

   ![image-20230320145125319](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201451380.png)

   常见的match函数包括

   - Dot product Attention

     <img src="https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201453041.png" alt="image-20230320145308999" style="zoom:80%;" />

   - Additive Attention

     <img src="https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201453836.png" alt="image-20230320145344799" style="zoom:80%;" />

通过match函数我们会得到每一个$\alpha_0$，然后对所有得到的结果去做一个softmax得到$\hat\alpha$，然后通过一个加权得出来输出$c^0$,$c^0$一般代表的是context vector，$c^0$会传递給spell（Decoder）去作为输入

![image-20230320145902214](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201459336.png)

5. Spell

   Distribution over all tokens的大小是根据我们所选的vocabulary的大小决定的，如果是a~z,那么他的size就是26，选取不同的token，数目也不同。这里面元素的值是经过softmax的，因此我们只需要

   <img src="https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201503496.png" alt="image-20230320150314441" style="zoom:80%;" />

在得到由$z^0$输出的output和$z^1$后，我们使用$z^1$重复进行上述步骤，不过此次$z^2$的生成还会借助第一次decode的结果

<img src="https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201509670.png" alt="image-20230320150958598" style="zoom:80%;" />

直到输出<EOS>，这是终止符号

![image-20230320151115429](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201511501.png)

6. Beam Search

   在decode选择最大概率的所代表的token是greedy search，但是这种decode出来的结果不一定是全局最优的，前一个输出会变成下一个输入，会导致输出的结果是不一样的

   ![image-20230320152216640](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201522713.png)

   最好的情况是绿色 -> 有点像强化学习的延迟满足

​		![image-20230320152324827](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201523907.png)

但是我们没有办法去搜集所有出现的概率，因此我们在每一步保留 $B$ 最好的路径，保持$B$步回头路，选择概率最大的

![image-20230320152514112](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201525179.png)

保留的越多，可能找出比较大的路径，但是计算量也会增大

7. Training

   使用cross entropy，希望我们的输出越来越接近类标C

   ![image-20230320152727018](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201527108.png)

​	decode的时候前一阶段的输出会作为第二阶段的输入，但是如果在训练的过程中不mask掉的话，那会导致结果越来越偏离，因此我们需要使用Ground Truth 作为下一个阶段的输入，不管前一阶段输出的是什么。这就是teacher forcing

![image-20230320153301693](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303201533753.png)

8. 一些其他的Attention

   Attention得出的结果是在下一步用还是在这一步用

   ![image-20230321090720123](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303210907260.png)

​		但是在语音中，人们两个都要

​		![image-20230321090827947](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303210908019.png)

9. LAS - Does it work？

   Yesss~

   端到端相较于以前的语言模型+文字模型的组合整体体积会小很多

10. Limitation of LAS

    不能做成online，因为依赖于上一时刻的输出

    输入的长度对模型的效果影响较大

    ![image-20230321092436191](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303210924255.png)