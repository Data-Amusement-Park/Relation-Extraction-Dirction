# 实验目标
验证模型对双向关系的区分能力
比如
```
[mary] is the mother of [tom]
```
上方有两个关系(mary, mother, tom),(tom, child, mary)
给定句子以及头实体尾实体，我们需要模型能够正确的进行关系分类。

# 实验数据集构造
实验采用了FewRel数据集作为原始数据来源，然后人工从中选择了5对双向关系，分别为
```
has part
r-has part
father
r-father
owned by
r-owned by
follows
r-follows
mother
r-mother
creator
r-creator
```
数据集的构建方式为，对于原有的5个关系`has part, father, mother, owned by, creator`, FewRel原始数据集每种关系含700个实例，我们将其350个头尾互换作为`r`关系实例

然后我们通过训练集：验证集=8:2的比例对数据集进行划分，并且保证了训练集和验证集每个类别的数量都占1/10

最后得到的数据在`reverse_data_train.json`和`reverse_data_val.json`文件中
# 数据预处理
为了测试不同的模型，这里处理了多版数据

## Data1.0
这一版数据通过以下命令生成
```bash
python datamodels/PCNNDataModel.py
```
这一版数据对于每个实例，存储了以下数据：
```
'str:token': [],  # 句子token列表
'num:pos1': [],   # token与pos1的相对位置
'num:pos2': [],   # token与pos2的相对位置
'str:label': str,  # 句子label
'num:label_id': int, # 句子label对应的id
'num:length': int,   # 句子长度
'str:h':str,         # 头实体字符
'str:t':str          # 尾实体字符
'var:h_span'         # 头实体头尾字符位置
'var:t_span'         # 尾实体头尾字符位置
```

# 实验结果
具体的badcase和混淆矩阵可以参考`badcas目录下的txt文件
## Data1.0
- CNN(不加入pos1, pos2的embedding, 经过max pooling): 38.21(ACC)
- PCNN(加入pos1, pos2的embedding, max pooling): 68.21
- PCNNEntity(在PCNN基础上，最后特征拼接了头实体表示平均和尾实体表示平均) 69.26


