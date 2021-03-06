# 2022-ZSYH-FinTech
20225月12日, 第一次正式参加数据比赛, 参赛人数共2000人, 最终排名前20%

## 经验总结
- xgboost和lgbm的参数调节
- 回归/分类时可以把一些分布比较广的数据限制在一定范围内
- 观察分布/对抗验证寻找毒特

本次比赛遇到的最大问题是模型在A榜上吻合很好,但是在b榜上掉的很严重.实际上,训练集与测试的不一致问题是经常面临的问题，在互金领域，市场环境变化，准入策略，额度策略的调整，会导致训练样本在时间维度上存在差异，因此在利用大规模样本，或者长期限样本来训练模型时，必然会存在训练集与测试集(或者当前线上打分模型)产生偏差. 比如量化金融领域经常用滚动训练的方式来避免时间效应.这次比赛是主要是有关于CUR的一些特征导致的.

#### Adversarial validation
Adversarial validation,网上的翻译是对抗验证.实际步骤如下:
1.构建一个样本的分类器，该二分类器的任务用于区分样本来源于训练集还是测试集。因此，需要为原始数据新增一个标签列，将训练集中的样本标记为0， 测试集中的样本标记为1，样本的特征是数据中已有的特征，或者衍生的新特征，生成新的训练数据集；
2.将新的训练数据集进行划分，保留部分样本作为该样本分类任务的测试集T， 利用分类算法(XGBoost, LightGBM)等对数据集进行训练，AUC作为模型指标；
3.在测试集T中进行验证，如果模型效果AUC在0.5左右，说明该样本分类模型无法区分样本来源训练集，还是测试集，说明原始数据中训练集，测试集分布是一致的；如果AUC较大，如0.9, 说明样本分类器很容易区分样本，间接说明训练集与测试集存在很大差异；
4.根据第3步的结论，对于分布一致的，正常对目标任务训练即可。对于分布不一致的，可以继续进行样本挑选的尝试。利用上述样本分类器模型，对原始的训练集进行打分预测，并将样本按照模型分从大到小排序，模型分越大，说明与测试集越接近，那么取训练集中的TOP N 的样本作为目标任务的验证集，这样即可将原始的样本进行拆分得到 训练集，验证集，测试集。那么线上模型验证时，在这样的数据上调参等得到模型，如果在验证集上效果稳定，那么应用在测试集上，大概率结果是一致的
Note: 第四点每太读懂


## A榜思路


## B榜思路
