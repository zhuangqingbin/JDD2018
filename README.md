# JDD-2018京东数字科技全球探索者大赛
[大赛链接](https://jdder.jd.com/index/jddDetail?matchId=3dca1a91ad2a4a6da201f125ede9601a)
## 数据集
赛题的数据包括人口流动、人口来源去向、移动设备用户占比三个样本，其中人口数目是经过预处理后的相对值，日期和地区代码均为模拟数据。具体数据说明如下


- 人口总决赛数据：时间跨度：20170523-20171104；**时间缺失**：20170818-20170823；**预测**：20171105-20171115

- 人口流动数据

| 字段名称 | 	样例数据 | 说明 |
| ------ | ------ | ------ |
| date_dt | 20180301 | 日期（年月日） |
| city_code | 70e1931bbdc17b6a5ed8e8fa258262b4 | 脱敏城市编码 |
| district_code | f4182c331ac63e3c460b36dc3fd70775 | 脱敏区县编码 |
| dwell | 1.612 | 当天驻留人数 |
| flow_in | 0.9212 | 当天流入人数 |
| flow_out | 1.4531 | 当天流出人数 |


- 人口转移数据

| 字段名称 | 	样例数据 | 说明 |
| ------ | ------ | ------ |
| date_dt | 20180301 | 日期（年月日） |
| city_code | b7aaa7d06ef82b84fba8c5b072eb325b | 出发城市编码 |
| district_code | 1be42486b669cf3f7b8a4bbc89f2b776 | 出发区县编码 |
| city_code | b7aaa7d06ef82b84fba8c5b072eb325b | 到达城市编码 |
| district_code | f36c87310bf361b3d6c8bfe532e17d60 | 到达区县编码 |
| cnt | 1.772 | 转移人数 |


- 用户占比数据（决赛数据）

| 字段名称 | 	样例数据 | 说明 |
| ------ | ------ | ------ |
| date_dt | 20180301 | 日期（年月日） |
| city_code | b7aaa7d06ef82b84fba8c5b072eb325b | 城市编码 |
| district_code | 1be42486b669cf3f7b8a4bbc89f2b776 | 区县编码 |
| dwell_ratio | 0.3 | 当天驻留人口中移动设备用户比例 |
| flow_in_ratio | 0.4 | 当天流入人口中移动设备用户比例 |
| flow_out_ratio | 0.5 | 当天流出人口中移动设备用户比例 |

## 数据处理
- 区间：跳跃序列识别，识别出较前一段时间序列跳跃15%以上的数据点，如果其后一段时间数据平稳，则标记为区间的跳跃点，
**预测问题取后一部分，缺失问题取前一部分**


- 异常点：窗口平滑取3、7、9，（该天的值-窗口3的均值）/窗口3的标准差，大于3，压缩到一个标准差；
大于2.5，压缩到0.75个标准差；大于2，压缩到0.5个标准差

- 将原始数据转换为对数形式、增长率形式


## 缺失

- 前后两段建立arima模型预测，尝试过使用向量自回归模型，用模型中所有当期变量对所有变量的若干滞后变量进行回归，
用来估计联合内生变量的动态关系（AIC、BIC选择阶数），但是效果并不好，可能是因为三个变量之间的联系并不显著。
再利用Xgboost来stacking结果。

- 合并`人口转移数据`作为新的特征，对每个区域分别建立xgboost模型，效果不如arima模型


## 预测

- arima模型

- 合并`人口转移数据`作为新的特征，对每个区域分别建立xgboost模型，效果同样不如arima模型

- 数据在周度存在规律计算出一周七天对该周的平均值比率，得到周几的占比序列和周均值的序列，利用arima模型预测这两个数，由此计算出预测值

- RNN





