# GMM原理
1. GMM模型：
每个 GMM 由 K 个 Gaussian 分布组成，每个 Gaussian 称为一个“Component”，这些 Component 线性加成在一起就组成了 GMM 的概率密度函数：

![](http://img.my.csdn.net/uploads/201211/19/1353293108_2454.png)

根据上面的式子，如果我们要从 GMM 的分布中随机地取一个点的话，实际上可以分为两步：首先随机地在这 K个Gaussian Component 之中选一个，每个 Component 被选中的概率实际上就是它的系数 pi(k) ，选中了 Component 之后，再单独地考虑从这个 Component 的分布中选取一个点就可以了──这里已经回到了普通的 Gaussian 分布，转化为了已知的问题。

那么如何用 GMM 来做 clustering 呢？其实很简单，现在我们有了数据，假定它们是由 GMM 生成出来的，那么我们只要根据数据推出 GMM 的概率分布来就可以了，然后 GMM 的 K 个 Component 实际上就对应了 K 个 cluster 了。根据数据来推算概率密度通常被称作 density estimation ，特别地，当我们在已知（或假定）了概率密度函数的形式，而要估计其中的参数的过程被称作“参数估计”。



2. 参数与似然函数：

现在假设我们有 N 个数据点，并假设它们服从某个分布（记作 p(x) ），现在要确定里面的一些参数的值，例如，在 GMM 中，我们就需要确定 影响因子pi(k)、各类均值pMiu(k) 和 各类协方差pSigma(k) 这些参数。 我们的想法是，找到这样一组参数，它所确定的概率分布生成这些给定的数据点的概率最大，而这个概率实际上就等于  ，我们把这个乘积称作似然函数 (Likelihood Function)。通常单个点的概率都很小，许多很小的数字相乘起来在计算机里很容易造成浮点数下溢，因此我们通常会对其取对数，把乘积变成加和 \sum_{i=1}^N \log p(x_i)，得到 log-likelihood function 。接下来我们只要将这个函数最大化（通常的做法是求导并令导数等于零，然后解方程），亦即找到这样一组参数值，它让似然函数取得最大值，我们就认为这是最合适的参数，这样就完成了参数估计的过程。

下面让我们来看一看 GMM 的 log-likelihood function ：


![](http://img.my.csdn.net/uploads/201211/19/1353293351_6835.png)

由于在对数函数里面又有加和，我们没法直接用求导解方程的办法直接求得最大值。为了解决这个问题，我们采取之前从 GMM 中随机选点的办法：分成两步，实际上也就类似于K-means 的两步。





3. 算法流程：

1.  估计数据由每个 Component 生成的概率（并不是每个 Component 被选中的概率）：对于每个数据 x_i 来说，它由第 k 个 Component 生成的概率为

![](http://img.my.csdn.net/uploads/201211/19/1353293606_4893.png)
其中N（xi | μk,Σk）就是后验概率:

![](http://img.my.csdn.net/uploads/201211/19/1353294259_8709.jpg)

2. 通过极大似然估计可以通过求到令参数=0得到参数pMiu，pSigma的值。
EM算法详解：http://www.cnblogs.com/jerrylead/archive/2011/04/06/2006936.html

![](http://img.my.csdn.net/uploads/201211/19/1353293943_5189.png)

其中 N_k = \sum_{i=1}^N \gamma(i, k) ，并且 \pi_k 也顺理成章地可以估计为 N_k/N 。

3. 重复迭代前面两步，直到似然函数的值收敛为止。
