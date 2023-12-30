## Perceptron Hypothesis Set

### Formulate in Linear Algebra

![[20-Learning-to-Answer-Y-N-hypothesis-set.png]]
- 每个客户的数据都组成一个向量 $\bf x$ ，其中的每一个分量 $x_i$ 即是客户的特征，如年龄、年薪、工龄、负债情况……，
- 对每一个分量 $x_i$ 赋以权重 $w_i$，二者相乘后求和，即可得到该客户的特征——从而验证能否通过批准信用卡的阈值 *threshold*，
- 对客户特征与阈值 *threshold* 取差值，差值为正即允许批准信用卡的申请，为负则不批准，若是为 0？事实上，这种情况很少见，剔除后也不会造成影响；
- 这个假设函数 $h(\mathbf{x})$ 如何用线性代数中的向量内积表示？
	- ![[20-Learning-to-Answer-Y-N-vector-H.png]]
	- 关键在于，延伸一个第 0 维的分量 $w_0$ 和 $x_0$ 作为 *threshold* 和*label*；

### Linear Classifier

- 进一步地，这个假设函数 $h(\mathbf{x})$ 可否图形化？
	- ![[20-Learning-to-Answer-Y-N-linear-classifiers.png]]
	- 若只取两个分量，即得到二维空间 $\mathbb{R}^{2}$（取更多分量则得到更高维的图形 $\mathbb{R}^{n}$），
	- 用户的特征向量即是这个二维空间中的一个点 $(x_i,y_{i})$，
	- 对用户的判断称作加标签 *label*，+1 记作蓝色圆圈，-1 记作红色叉,
	- 假设函数 $h(\mathbf{x})$ 在二维空间中的图形就是一个直线，对直线两侧的点进行预测，一侧为正，一侧为负,
	- 这就是==线性分类器==。

### Application: detect spam messages

![[20-Learning-to-Answer-Y-N-linear-classifier-app.png]]

## Perceptron Learning Algorithm

### 选择合适的 g 

现在的问题就是如何从无穷大的 $\mathcal{H}$ 中尽可能高效地取出尽可能准确的 *g* 作为 *f* 的估计：
- *f* 是未知的，$\mathcal{H}$ 的空间又是无穷大的，因此取出准确的 *g* 相当困难,
- 目标是找出理想的 *g* ，使得 $g(\mathbf{x}_{n})=f(\mathbf{x}_{n})=y_{n}$ ,
- 一个好的方法是，先任意地选择一个 *g* 作为起始，然后根据数据样本逐步地修正使用它发生的错误，直到发生错误的概率足够小——起始的估计称为 $g_0$，其有权重向量 $(w_0,w_1,w_2,...,w_N)$，因此简化地将起始估计取其权重向量，并称作 $\mathbf{w}_0$。

### 知错就改

现在，从 $\mathbf{w}_0$ 开始，逐步地修正它：
- ![[20-Learning-to-Answer-Y-N-PLA.png]]
- 如果 $\mathbf{w}_0$ 并不是当前数据集中一个完美的估计，那么就一定存在一个样本 $(\mathbf{x}_{n(t)},y_{n(t)})$ 能够使对其预测的结果和真实的标签并不相同：$\text{sign}\left(\mathbf{w}_{t}^{T}\mathbf{x}_{n(t)}\right)\ne y_{n(t)}$ ➡ 这就是“犯错”
- 如何“修正错误”？如果实际的 label 是+1，但是预测却是 -1，那就在预测的权重向量 $\mathbf{w}_0$ 上加一个 $y\mathbf{x}$，这里 *y* 是+1；反之亦然
- 那么每一轮 *t* 都进行这样的错误点查找、纠正，直到越来越接近正确值、不再预测错误，那么最后一轮的这个 $\mathbf{w}_{PLA}$ 就是在训练集中最佳的 *g* 

周期性地查找 PLA：
![[20-Learning-to-Answer-Y-N-cyclic-PLA.png]]

![[20-Learning-to-Answer-Y-N-funtime.png]]
- 这个练习题能够进一步理解每轮迭代都进行了怎样的工作 —— 第三个选项代表了下一轮的分数更大，超出阈值更多，也更有可信度。

### 思索

尽管这样确实能够找到一个 $g\approx f$，但仍有几个问题值得思量：
- 一共要进行多少个周期？
- 这个循环是无限的吗？可否确定会停止？（停止即在训练集上的预测不再出错）
- 得到的 *g* 确实能够代表 *f* 吗？

## Guarantee of PLA

## Non-Separable Data