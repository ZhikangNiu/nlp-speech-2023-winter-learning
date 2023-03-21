# [DLHLP 2020] Speech Recognition (3/7) - CTC, RNN-T and more

![image-20230321202611811](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303212026870.png)

## CTC

相当于只有Encoder，可以做成online的，并且加入了blank token 

![image-20230321193253836](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303211932955.png)

输入T个声学特征，我们输出T个tokens（忽略下采样），然后我们根据blank符号进行合并

### CTC的训练

 需要使用Blank符号去allignment数据，穷举所有的allignment的数据去训练，需要对输出的结果做下postprocess。

### CTC的问题

1. 每一次的输出之间是独立的（这个假设很强并且在语音中也不合理
2. 每一次只计算一个向量 

## RNN-T

 CTC -> RNA -> RNN-T

![image-20230321195314551](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303211953618.png)

RNA相当于加了一个RNN使得输出可以与前一个时刻产生关系，RNA在做变化可以成为RNN-T

![image-20230321200026542](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303212000609.png)



一个h可以不断被输出，直到输出Blank符号，一个完整的流程如下

![image-20230321200126493](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303212001563.png)

得到所有的结果，然后去除blank符号

若干个T输入，会有若干个T的blank

### RNN-T的问题

数据的对齐，在训练时我们需要考虑allighmnet，在训练时，也会和CTC一样穷举所有的allighment

他可以加一个LM，会无视blank符号

![image-20230321202042962](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303212020039.png)

![image-20230321202111094](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303212021176.png) 

## Neural Transducer

![image-20230321202313355](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303212023427.png)

![image-20230321202439558](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303212024626.png)

这个window在原始论文里面测试了5~30（没有Attention），存在Attention后Windows size就无关了

![image-20230321202542983](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303212025057.png)

## MochA

![image-20230321202740670](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303212027733.png)

![image-20230321202851444](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202303212028522.png)