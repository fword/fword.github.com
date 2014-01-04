---
layout: post
title: PCA和LDA理解以及在实验中的使用
category: 
 - Machine Learning
---

**PCA原理**  
我们的目的是求解$u$，使得将X投影到最主要的成分上，即协方差最大的维度上。
\begin{equation} Y=uX \end{equation}
其中X是R\*M的，Y是1\*M的，$u$是1*R的。   
最后其实就是优化如下问题：
\begin{equation} u^TSu+\lambda(1-u^Tu) \end{equation}   
其中$S$是X的协方差，然后根据凸属性，令偏导等于0，可得  
\begin{equation} Su=\lambda u \end{equation}
我们选取$S$的最大的特征向量就是$u_1$。如果想保留其他的维度，可以依次保留其特征向量。  
我这里省略PCA大部分的推导过程，详情参考PRML12.1 p561，或者[这里][1]。  

**LDA原理**  
我们的目的是求解$u$，使得投影后类内方差尽量小，类间的方差尽量大。  
\begin{equation} Y=uX \end{equation}  
其中X是R\*M的，Y是1\*M的，$u$是1*R的。我们可以从下面这张图片上直观上理解下它的目的： 
![1](/assets/images/2014/pca_lda.gif) 
而该问题是优化如下问题：  
\begin{equation} \frac{u^TS_Bu}{u^TS_Wu} \end{equation}  
同样由于不考虑w的大小，加入限制项后可得：  
\begin{equation} S_W^{-1}S_Bu=\lambda u\end{equation}    
则对$S_W^{-1}S_B$求特征向量就可以得到$u$。 求出$u$之后就可以将原来标注的数据分别映射到这个线性空间中，然后把需要分类的数据也映射过去，看离哪个类的中心近就分到哪个类。这也就是为什么LDA是supervised learning.

详细推导步骤参考PRML4.1.4 P186，或者[这里][2]  

**补充1**  
这里提一下二类的$S_B$和多类的$S_B$是不一样的，二类的$S_B$是：
\begin{equation} S_B=(m_1-m_2)(m_1-m_2)^T \end{equation}  
其中$m_i$表示X中类i的均值。由于$m_1-m_2$是行向量， $u$也是行向量，则$(m_1-m_2)^Tu$是个scalar。

\begin{equation} S_W^{-1}S_Bu=S_W^{-1}c(m_1-m_2)=\lambda u\end{equation}    

则可以直接求出u为  
\begin{equation} u=S_W^{-1}(m_1-m_2) \end{equation}  
多类的$S_B$的计算方式是
\begin{equation} S_B=\sum_{k=1}^KN_{k}(m_k-m)(m_k-m)^T\end{equation}  
其中$N_k$ 表示第k类内元素的总数,$m_k$表示第k类内元素的均值，$m$表示所有元素的均值。这个式子的来历是，类间的方差等于所有方差减去类内方差:
\begin{equation} S_{all}-S_W=\sum_{n=1}^N(x_n-m)(x_n-m)^T-\sum_{k=1}^KS_k\end{equation}  

**补充2**   
关于求[矩阵的协方差][3]，其计算方法是：  
>协方差(i,j)=（第i列的所有元素-第i列的均值）\*（第j列的所有元素-第j列的均值）  

所以求矩阵的的协方差可以先将样本进行中心化，然后乘以其转置矩阵。则X的协方差为  
\begin{equation} S_x=\frac{1}{n-1}XX^T \end{equation}
同理：
\begin{equation} S_y=\frac{1}{n-1}YY^T \end{equation}
则：
\begin{equation} S_y=\frac{1}{n-1}(uX)(uX)^T =\frac{1}{n-1}uXX^Tu^T=uS_xu^T\end{equation}
 

**在matlab中使用PCA**  
>[COEFF,SCORE,latent] = princomp(X)

COEFF是X矩阵所对应的协方差阵V的所有特征向量组成的矩阵，即变换矩阵或称投影矩阵。(principal component coefficients)   
SCORE是对主分的打分，也就是说原X矩阵在主成分空间的表示。( the principal component scores)  
LATENT协方差矩阵的特征值。(a vector containing the eigenvalues of the covariance matrix of X.)   
用原矩阵x\*coeff(:,1:n)就是新数据，其中的n是想降到多少维。具体要降到多少维度，需要看自己的需求，具体可以根据LATENT矩阵来判断
>percent = 100\*latent/sum(latent(:,1:n));

一般的话，当percent达到90%以上我们认为属于符合要求的。

详见:[matlab官网][4]    

**在matlab中使用LDA**  
>class = classify(sample,training,group)  

sample是需要分类的数据，training是训练的数据，group是分组信息，class是预测的标签。由此可见sample必须和training有相同的列数，因为维度必须相同。training和group必须行数相同，因为每个元素要分一类。

详见：[matlab官网][5]


  [1]: http://www.cnblogs.com/Leftnoteasy/archive/2011/01/08/lda-and-pca-machine-learning.html
  [2]: http://www.cnblogs.com/jerrylead/archive/2011/04/21/2024384.html
  [3]: http://blog.csdn.net/ybdesire/article/details/6270328
  [4]: http://www.mathworks.cn/cn/help/stats/princomp.html
  [5]: http://www.mathworks.cn/cn/help/stats/classify.html