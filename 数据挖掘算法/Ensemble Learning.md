# Ensemble Learning

集成学习包括bagging 和 boosting两大类

集成方式：

Voting : 

- Majority voting：RF
- Weighted Majority voting : adaboost

Learning Combiner : 

- General Combiner : stacking
- Piecewise Combiner : Regionboost

**不同的分类模型**：

- 训练集不同
- 特征不同

# Bagging



- 随机取样，随机特征
- 训练分类器
- 投票
- 无需指定训练集和测试集，自然有$\frac{1}{3}$未被选中，可以作为测试集

![image-20200910164818853](/Users/xuepipi/Library/Application Support/typora-user-images/image-20200910164818853.png)

- 多少棵树：500+

- 不需要交叉验证，直接利用OOB(out of bag)里边的样本用于测试

- 可调参数少
- 无需属性选择

降低方差

# Stacking

核心思想：训练弱分类器投票的权重

Bagging的升级版

# Boosting

- 串行的集成

- 根据误差，调整训练样本
- 初始模型可以很弱

提高的模型是准确度

## Adaboost

![image-20200912093600941](/Users/xuepipi/Library/Application Support/typora-user-images/image-20200912093600941.png)

adaboost没有参数