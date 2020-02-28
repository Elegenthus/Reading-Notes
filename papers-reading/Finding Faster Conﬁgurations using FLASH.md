# Finding Faster Conﬁgurations using FLASH

* 刊物：IEEE Transactions on Software Engineering（软件工程/系统软件/程序设计语言 A类期刊）
* 主题：为软件系统（用workload代替）寻找合适的配置
* 方法：建立一个序列模型，根据当前配置去决定下一步探索的配置，使用CART决策树模型
* 优点：能够解决`single-object`以及`multi-object`优化问题，大幅度降低measeures的数量

* 背景
    * 很多软件使用默认配置
    * 了解具有巨大配置空间的软件系统的配置很具有挑战性
    * 探索大量的配置通常是不可行，由于长时间的benchmarking
* 已有研究
    * 使用机器学习建立配置空间模型
        * 该模型是按顺序构建的，其中随机抽取新配置，并使用保留集来测量模型的质量或准确性。这些保留集通常占很大的空间并且甚至在模型完全构建之前，都需要进行评估（即测量）。这使得在实用背景下不可进行，因为生成保留集很昂贵
    * 过去的序列模型方法依赖于Gaussian Process Models (GPM)来反映配置评估情况
        * GPMs并不适用于有超过12种以上配置选项的软件系统
