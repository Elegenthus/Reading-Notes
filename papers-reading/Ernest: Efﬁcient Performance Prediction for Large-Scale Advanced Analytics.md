# Ernest: Efﬁcient Performance Prediction for Large-Scale Advanced Analytics

## Abstract
Various workloads on cloud computing infrastructure is rapidly developed. People now face the challenge of running these applications on shared infrastructure. 
Choosing right hardware configuration improves performance and cost well. What's more, a number of jobs have predictable structure in terms of computation and communication. Based on the two points above, we can build performance model on small sample datas and predict job performance on larger datasets and cluster sizes. So we build a performance prediction framework named Ernest.

## Introduction
A rapid development of large-scale advanced analytics implement complex algorithmn in applications, which differ from traditional analystics workloads. 
Running time can be determined by the configuration of the instances. That is to say , we can infer the cost of running a given job based on the configuration. So choosing the right configuration can improve performance at the same cost.

The main contribution
* design a performance prediction framework Ernest to work with unmodified jobs ,which uses optimal experiment to lower overhead.
* Ernest can detect model's inappropriation and use small extension to model complex workloads
* Ernest is accurate.

* purpose: to build a model that will predict the execution time for any input size,number of machines for given job

选多少数据，用多少台机器去跑任务，能够在最短时间内完成任务

训练时间预测模型 -> 线性函数+最小二乘法
再根据 feature 选出集群规模 -> 计算协方差
根据选出的集群规模 预测时间
