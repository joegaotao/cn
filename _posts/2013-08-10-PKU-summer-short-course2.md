---
date: '2013-08-10'
layout: post
title: PKU暑期高维统计学习心得(II)
categories:
- 感想
tags:
- PKU
- 统计
- 高维
- 老师
---

### 前言

距上一篇时间颇长，不过继续Jiashun老师的讲课心得。上一篇谈到稀疏、弱信号的一种处理框架——Higher Criticism，在分类、聚类等领域可以有比较好的应用。具体如何应用，此处不详谈，大家可以看看他第二节课的PPT以及该篇论文。在第二节课结束时，他提了一个结论：

> Surprisingly, penalization methods (e.g., the L0-penalization method) are not optimal for rare/weak signals, even in very simple settings and even with the tuning parameters ideally set。

也就是说在稀疏、弱信号下，由L0衍生出来的方法并不是最优的，比较容易出问题。虽然我依稀记得某些论文模拟显示信噪比过低时候不少penalty方法结果并不太好，不过Jiashun老师的这个结论还是让我比较吃惊，毕竟被很正经的提出来了，而且他还有相对的解决方案！着实让我很感兴趣。

<!-- more -->

### Donoho的不确定原则（Uncertainty Principle）与信号恢复
Jiashun老师说，关于信号恢复最早应该可以追溯到Donoho在1989年的论文**Uncertainty Principle and Signal Recovery**。在这片文章中，Donoho给出了类似于海森堡测不准原理的不确定原则（UP）。海森堡测不准原理通俗来讲即微观粒子某些物理量不可能同时被精确测量准确，一个量越确定，另外一个量的不确定程度就越大。Donoho的不确定原则通俗来讲即，离散时间点 $t = 0, 1, \ldots, n - 1$ 有观测 $Y_t$，做傅里叶变换有

$$
\hat{Y}_w = \frac{1}{\sqrt{n}}\sum^{n-1}_{t=0}Y_t e^{-2\pi it/n}, \quad w = 0, 1, \ldots, n - 1
$$

用 $T$ 和 $W$ 分别表示 $Y_t$ 和 $\hat{Y}_w$ 中的非零的位置，那么就会得到一个不确定原则，

$$
|T|\cdot|W| \geq n, \Longrightarrow |T| + |W| \geq 2\sqrt{n}
$$

直观的解释和测不准原理类似，当时域 $T$ 上的非零点位置很稀疏时，那么关于频域 $W$ 上的非零点就不会稀疏，他们被一个 $2\sqrt{n}$ 的下界给bound住了。也就是说**$T$和$W$不能同时太稀疏**！虽然这可能是自然界非常普遍的规律，但是我有些疑惑，这个原则对于理解L0有什么意义？

在信号恢复中有这么个结论，观测 $Y_t$ 可以完美地被两组基给表示出来跳点基（spikes）和正弦基（sinusoids）

$$Y_t = \text{Spike}(t) + \text{Sinusoid}(t), \,\, t = 0, \ldots, n - 1$$

现假设一个无噪音的模型

$$
Y = X\beta = \sum_{\gamma}\beta_{\gamma}\phi_{\gamma}, \quad p = 2n, \quad X = [\Phi, \Psi]
$$

其中 $\Phi$ 和 $\Psi$ 分别是两组基。$[\Phi, \Psi] = [\underline{\phi_1, \phi_n}, \underline{\phi_{n+1}, \phi_{2n}}]$，此处令

* $T \subset \{1, 2, \ldots, n\}$, 为“时域”，
* $W \subset \{n+1, \ldots, 2n\}$, 为“频域”，
* 要找到$\beta$非零的位置，且具有稀疏性 $|T| + |W|\ll n$

目标就是给定 $(X, Y)$，来恢复稀疏的 $\beta$，此时根据Occam's Razor的原则，我们相信真实的 $\beta$ 应该是最稀疏的。转化为具体形式就是 $\ell_0$ 惩罚，

$$
(P_0): \quad \min\|\beta\|_0, \quad \text{such that } Y = X\beta
$$

虽然基于Occam's Razor原则，那么 $\ell_0$ 的稀疏解是不是唯一的呢？答案是唯一的。之前的UP原则已经暗含了：

** 对于$Y = X\beta$，不可能同时存在多个稀疏解。**

当然这个唯一性也是有条件的，当 $|T| + |W| < \sqrt{n}$ 时，$(P_0)$ 会有唯一解，$\ell_0$ 惩罚是最优的。如何理解这个upper bound的条件呢？

直观理解，结合不确定原则，时域与频域上非零稀疏个数至少是 $2\sqrt{n}$个，结合UP的那个乘法与加法的不等式，稀疏可以定义为 $|T| + |W| = 2\sqrt{n}$，且 $|T| = |W| = \sqrt{n}$，如果加上个两者之和最多少于 $\sqrt{n}$ 个，也就说明了时域、频域不能同时太稀疏，此时只有一个域上稀疏，那么这种恢复是唯一的。

如果要证明，也比较简单，形式推论如下：假设同时存在两个稀疏解 $\beta_1$ 和 $\beta_2$，那么做差 $\sigma = \beta_1 - \beta_2$ 也是一组稀疏解对于 $0 = X\sigma$，同样要满足UP条件，那么就有
$$
\begin{split}
2\sqrt{n} \leq & |T(\sigma)| + |W(\sigma)| \\
\leq & |T(\beta_1)| + |T(\beta_2)| + |W(\beta_1)| + |W(\beta_2) \\
< & \sqrt{n} + \sqrt{n} = 2\sqrt{n}
\end{split}
$$
可以看到与条件矛盾，那么也就是说不会存在两组稀疏解，$\ell_0$ 的解是唯一的。

**Remark**：

正是由于有这样的结论，所以一直以来我们都相信基于 $\ell_0$ 的稀疏解是最优的，但是我们可能忽略了模型一个关键的假设： 

> **模型为无噪音，或者是信噪比很高，噪音影响很小**

所以回到稀疏、弱信号的场景，我们就会很有理由怀疑基于 $\ell_0$ 惩罚的方法以及相关的衍生的$\ell_1$惩罚的解是最优的吗？如果不是，如何做才可以处理这种稀疏、弱信号。

首先我们阐述Occam's razor对于稀疏弱信号尝试是不太合适的。Jiashun老师给出了一个图形很好的阐述所存在的问题：

![occam](http://www.flickr.com/photos/45187658@N03/9478935170/)

从图中可以看到，如果没有噪音，或者噪音比较小时，恢复较大的真实的信号是很好的，不过当信号很弱，信噪比比较高时候，那么这些真实信号就会被一些噪音包围，而我们也无法分辨出来。用Occam's razor去挑选最简单的，显然效果会比较差。

另外一个问题，对于稀疏、弱信号，根据之前的Phase Diagram的信号恢复区域划分，以及上面这个图形，可以看到精确恢复已经是不可能了，那么用如下定义的*Oracle property*也就不再合适：

$$\min_{\beta} \,\, P(\hat{S} \neq S(\beta))$$

即找出来的非零的系数与真实非零的系数（信号）是同一个的概率非常高，趋近于1。可以看到这种损失函数对于并不强调对于弱信号的发掘，只要大部分强信号找到了就差不多了。一个更为直接的损失函数定义可能是所谓的Hamming距离

$$\min_{\beta} \,\, \text{Hamm}_p(\hat{\beta}, \beta) = \sum^p_{i = 1}(\text{sgn}(\hat{\beta}_i) \neq \text{sgn}(\beta_i))$$

由于关注所有系数（系数为0和系数非0）的损失，所有强、弱和无关信号都纳入了优化的目标中，尽量不误估系数为0的和系数非0的，所以要求比Oracle property更为严苛，希望不放过一丝蛛丝马迹。但是问题也将会变得更为复杂了！

讨论了这么多，费了很大劲说明之前深以为然的 $\ell_0$ 对于稀疏、弱信号不再是fundamentally correct了，那接下来有什么办法可以解决呢？推翻旧王朝相对来说还容易点，重新建个新王朝却更难了。Jiashun老师研究了那么久，自然有些办法来处理的。

### Graphlet screening
Jiashun老师开头先讲了个故事，说有次Fan他们来他们那里做分享，很开心的分享他们关于screening、SIS等方面的工作，Jiashun老师自然先不了解Fan做的SIS和ISIS工作，对于screening也构想了自己的方法，但是当他听完Fan老师讲完他的思路后，他大腿一拍，“这不和我的思路一个样嘛！”

如果诸君了解Fan老师在screenning方面的工作，那么就不会陌生Jiashun老师的思路。与Fan老师的SIS、ISIS思路相同，对于高维问题分两步走，一步screening得到一部分变量，然后对这些变量用些比较精细的方法诸如SCAD惩罚再来挑选，再稍微对第二步迭代调整下，可移除那些假非零系数（假信号）。而Jiashun老师的变量选择的方案也是一个两步方法。

对于细节此处不详述，我只是谈谈理解。Jiashun老师有两个很重要的想法：

1. 相关性不是噩梦，对于估计可能会降低估计效率（相当于样本减少），但是对于信号检测，却是是一个福音。我举个例子来理解，比如舆情监测，在浩如烟海的网络上有个人喊了句“打到XX”，这信号弱的简直没人知道，但是如果有几个与这个人存在某种关系的人，他们也跟着喊了句“打到XX”，虽然都很弱，但是还是起到了增强信号的作用，相当于几个弱信号汇集一起，让其中一个人发出了怒吼“打到XX”，那么自然这个时候信号检测相对会容易些，而边上的几个弱信号也顺便被检测到。因此要善于利用相关性来增强信号，对于稀疏、弱信号是一个很重要的手段。

2. 信号稀疏，变量之间的相关性也是稀疏的。将变量间的相关性看做一个图，也就是说信号会分散到很多小子图中，而且这些小子图内部相关性强，而相互之间没什么联系。用形象的话来说，就是信号分散于很多小岛屿上，具体哪些小岛屿有信号还不太清楚，但是我知道信号就分散这些小岛上，小岛上处处散发着信号的微光，而小岛屿之间对另外一个信号的发现没有任何帮助。好比中国大地星星之火隐约可见，却不知他们各自藏身于何处。Jiashun老师对变量间的相关性用一个阈值来控制（screening），以达到图的稀疏（sparse）和seperable（分离）的效果。我个人觉得这个假设有点强，将大图划分为多个独立的子图，这种方法略显有些暴力啊，你想这些星星之火间真的没有联系么？好吧，就当没有什么联系吧，因为我也没啥特别好的想法:(

因此整体思路就是：

1. 假设变量形成的图 $\mathcal{G}$ 是稀疏的，信号可分解为多个独立子图 $\mathcal{G}_s$（最大连通子图）。于是利用相关性对原来的观测进行变化，通过这种方式增强信号后对图$\mathcal{G}$ 进行screening，即可获取支撑集，然后分解为多个子 $\mathcal{G}_s$；
2. 在这些子图上，再利用加惩罚的MLE方法来估计、拟合即可。

**稍微细节点阐述如下**：

首先有个概念称作Graph of Strong Dependence(GOSD)，即有图$\mathcal{G} = (V, E)$，两个节点有边的条件是$|G(i, j) \geq \frac{1}{\log(p)}|$。因Gram matrix $G = X'X$稀疏可认为图$\mathcal{G}$稀疏。，从GOSD可以引出一个很关键的假设：$\mathcal{G}_S$可以分解为许多不相连接的小块，每个小块都是最大连通子图，即上面的第二点重要的想法。不过图稀疏的，但并不意味着图的结构很简单，稀疏图的小块同样可以很复杂。然后定义$\beta$的支撑集
$$S = S(\beta) = \{1 \leq i \leq p, \beta_i \neq 0\}$$
由支撑对应的节点可以形成子图$\mathcal{G}_S$。

要估计支撑集的办法与SIS类似，肯定要粗暴点，不能用加惩罚类的最小二乘法（PLS）等来挑选，PLS做下一步的精挑和调整是比较合适的。Fan针对他的模型提出的思路是用边际似然来做对变量重要性排序然后挑选前面重要的变量，这个方法非常常见，没有什么特殊。不过前面讨论过，这些方法都是基于信号比较强的假设提出来的，多余rare/weak的信号是第一步screening就会将信号给踢出大门外，即使下一步再来调整，也基本无望重新找回来。

在Screening步中，对于rare/weak信号的挑选此时就需要利用上面提到的第一点重要想法，利用相关性来筛选rare/weak信号。

**1. Univariate Penalized Screening(UPS)**

Jiashun老师提出假设简单模型
$$
Y = X\beta + z, \quad z \sim N(0, I_n)
$$
UPS方法不是直接对X或Y做screening，而是对变化数据 $\tilde{Y} = X'Y$ 做 screening，这其实一种增强信号的处理，以便于挑选rare/weak信号，它与之前Jiashun老师提出 Innovated Higher Criticism 有很强的的关联，先阐述 Innovated HC 方法以便于理解，Innovated HC 也是一种对于 rare/weak 信号挑选比较合适阈值的方法。对于阈值选择问题：

* 直接的想法就是用[上一次提到的HC方法](http://joegaotao.github.io/cn/2013/07/PKU-summer-short-course/)，假设各个信号独立无相关性来挑选信号，即 $\beta \sim N(\mu, I_p)$。但是实际情况多数各种信号噪音间有相关性 $\Sigma \neq I_p$，因此直接用HC似乎不那么美好。
* 另外一种想法就是常见的Whitening方法，用 $\Sigma^{-1/2}\beta$ 线性变化使得变量相关性去掉 $\Sigma^{-1/2}\beta \sim N(\Sigma^{-1/2}\mu, I_p)$，然后再用HC方法来挑选。
* 还有一种想法就是所谓的Innovated HC，应用HC于如下变化 $\Sigma^{-1}\beta \sim N(\Sigma^{-1}\mu, \Sigma^{-1})$。

上面的三种方法貌似都有一定道理，但是哪种最好呢？对于相关性，我们很多时候认为对估计是不太好的事情，但是对于信号检测，这却是有益的，上面我已经有阐述，所以直接HC会损失不少信息，那么后面两种方法看起来会更好些。当然看是看不出来的，简单计算下便可得出答案。

假设 $\mu$ 要不是为0，要不为信号 $\tau > 0$，$\Sigma$ 是一个 $2\times 2$ 分块矩阵
$$
\begin{pmatrix}
1 & h \\
h & 1
\end{pmatrix}
, \quad \|h\| < 1
$$

稍微一个简单的矩阵计算，三种边际的信噪比（Marginal SNR）：

* 纯HC：$\tau$;
* Whitening后HC：$[2/(1+\sqrt{1 - h^2})]\tau$;
* Innovated后HC：$[1/(1 - h^2)]\tau$。
可以看到变换后，Whitening  和InnovatedSNR 都增加了，但是 Innovated 的增加的更多。用图形来阐述就是下图，左图是原始信号，而右边是经过变换后的信号，原弱信号增强了，当然边上的噪音也会有些增加了。

![innovated](http://www.flickr.com/photos/45187658@N03/9478935194/)

这种 Innovated HC 与 UPS 方法的联系在于 $X$ 是随机阵时，有一个完美的随机阵版本（Stein's normal means model）$X \overset{i.i.d}{\sim} (0, \frac{1}{n}\Omega)$, 而Stein正态均值模型有 $W \sim N(\beta, \Sigma)$，其中 $\Sigma = \Omega^{-1}$，于是乎 $X'Y = X'X\beta + X'z$，而 $X'X\beta \approx \Omega\beta$ 和 $X'z \approx N(0, \Omega)$，于是 $X'Y \approx N(\Omega\beta, \Omega)$，即Innovated HC。所以UPS方法做Screening会保证一些较好的性质，如Sure Screening（信号基本都在筛选出来的信号中），Separation After screening(SAS)（存留的信号满足GOSD，可以拆分多个不相连的子块）。

虽然这种UPS的方法虽然可以保证较好的性质，不过需要较强的条件，而且还会出现**信号抵消（Signal cancellation）**的现象。（该现象在[Wasserman，2009年论文的第五页](http://www.stat.cmu.edu/~roeder/publications/wr2009.pdf)举了一个小例子说明这个现象，主要原因就是相关性的介入，虽然真实信号还比较强，但相关性使得估计的信号被抵消而减弱使得screening时候信号被当做噪音给去掉了，导致False Negative上升了。）这也是一个比较严峻的问题，会导致把许多真信号给删掉了。为了解决这个问题，我们就需要不仅仅只利用UPS，还需要利用之前的Gram matrix $G = X'X$ 蕴含的稀疏图信息，尽量防止Signal cancellation现象出现。

**2. Graphlet Screening（GS）**

一个直接而暴力的想法，就是扩展UPS，将单变量的screening变成多变量的screening，即多个变量一起满足某个阈值限制时才将这些变量选入，从单变量试到m变量的screening，如果某些变量一直出现，则将其认定为蕴藏了信号，留作下一步的cleaning。这个想法目的就是想消除**Signal cancellation**，看起来是*比较有希望*的，不过坏处也显而易见，计算量的急剧增加，将涉及至少$\binom{p}{m}$个子模型的计算，而且也很可能使得选出来的变量更难在下一步中将有用变量与无用变量分离开。因此想法虽然还不错，还是还需要打磨下，于是便有了Jiashun老师Graphlet Screening的方法，改进的想法如下：

* 只考虑需要考虑的有价值的的子模型，贯穿GOSD的想法到screening和cleaning中，只关注内部有强相关的子图，即仅利用Gram matrix中的 $X$ 来大大削减所需要考虑的子模型个数；同时还能解决信号抵消的问题。

GOSD降低计算量是因为假设图是 $k$ -sparse的，这样就从原来的 $O(p^m)$ 降到了差不多 $O(k^m)$ 的级别。具体的定理证明可详见Jiashun老师的论文和slides，此处再稍微提下GS的算法过程：

* 先利用GOSD获取稀疏图，然后在该图上选取子模型，每个子模型的节点数$\mathcal{I}_0不超过 $m$ 个，对这些子模型进行screening。由于每个节点于自身互通，于是最初的的子模型都是单节点。
* 然后初始化留存节点集 $\mathcal{U}^{\star}_p$，对每个子模型地节点进行检验，是否要选入该子模型中的部分变量。想法是如果Y在整个该子模型 $\mathcal{I}_0$ 变量上的投影的平方和，与在该子模型与留存节点集共有变量 $\hat{F}=\mathcal{I}_0 \cap \mathcal{U}^{\star}_p$ 上的投影平方和，两者的差值如果大于某个阈值（经验性），则将该子模型的变量与留存变量集的差集变量选入，更新留存变量集（其实是一种适应性卡方检验）。该步骤的直观理解是如果两者的共有变量解释能力所占比重比较小，感觉像噪音，则说明另外一部分变量在该子模型中很可能是信号。
* screening后剩下的变量分解到各个自己的子模型 中$\mathcal{I}_0$，对这些子模型分别做类似 $\ell_0$ 变量选择既可挑选各个子模型的有用信号了。下面模型中的系数还有个阈值限制，需要大于$v^{gs}$。$P^{\mathcal{I}_0}$ 表示在子模型 $\mathcal{I}_0$ 变量上的投影。
$$\|P^{\mathcal{I}_0}(Y - X^{\star, \mathcal{I}_0})\xi\| + (u^{gs})^2\|\xi\|$$

整体看来，过程还是显得有些繁琐，引入了不少参数，这些阈值参数有些给出了解析式子，有些比如 $v^{gs}$ 却比较难选择。算法给完后面免不了是不少理论性质的证明，在严苛的Hamming loss上可以最优，比lasso好，Phase diagram上表现也很好等等好的理论性质，不过这些我都没怎么细看了，一时看不明白也看不完呀！总之给我的印象就是还不错！不过遗憾在于相比HC框架的简约不用给代码，但是这个模型还是做个R包或者给点代码吧，践行Zou Hui的**统计产品**理念看来还是有必要的:)

### 总结
总之，Jiashun老师的想法就是想尽可能地挖掘变量间的关系来帮助变量选择。之前Zou hui的[Adaptive lasso](http://pages.cs.wisc.edu/~shao/stat992/zou2006.pdf)，Bulhman的[Multi-step变量选择](http://arxiv.org/pdf/0808.1013.pdf)，Longzhe利用[network做laplacian惩罚](http://bioinformatics.oxfordjournals.org/content/24/9/1175.full)，还有相似的Cun-hui老师先学adjacency matrix，然后[MCP+laplacian惩罚来做变量选择](http://arxiv.org/pdf/1112.3450.pdf)，以及今年在北京大学春季统计会议上Jinzhu老师提出的[preconditioning方法](http://arxiv.org/pdf/1208.5584.pdf)，用SVD的 $U$ 矩阵和 $D$ 矩阵来对 $X$ 做变换，也都是想对原始的变量进行挖掘，想办法在更弱的条件下来做更准确的变量选择，不过我更倾向于用图（变量关系）来提高变量选择的准确性，Witten和Tibshirani的[Scout方法](http://www-stat.stanford.edu/~tibs/ftp/WittenTibshirani2008.pdf)比较切合我的意图，不过现在看到Jiashun老师的思路和想法，不禁还是被深深吸引，在Gram Matrix上和变量变换上做文章，想法很好很深刻，何况还有之前的HC框架呢！尽管论文比较难读，涉及很多性质证明，我也基本没怎么细看，但是我想如果有可能，还是希望能够在这个基础上再继续做点东西吧，但愿:)


