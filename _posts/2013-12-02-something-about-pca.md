---
layout: post
title: PCA公式推倒以及在实验中的使用
category: 
 - Machine Learning
---

**PCA原理**  
我们的目的是求解P，使得
\begin{equation} Y=PX \end{equation}
其中X是R\*M的，Y是R\*N的，其中M>N；这里其实就实现降维度的目的。  
由于我们想要保留数据中那些方差最大的维度，所以首先要求[矩阵的协方差][1]，其计算方法是：  
>协方差(i,j)=（第i列的所有元素-第i列的均值）\*（第j列的所有元素-第j列的均值）  

所以求矩阵的的协方差可以先将样本进行中心化，然后乘以其转置矩阵。则X的协方差为  
\begin{equation} S_x=\frac{1}{n-1}XX^T \end{equation}
同理：
\begin{equation} S_y=\frac{1}{n-1}YY^T \end{equation}
则：
\begin{equation} S_y=\frac{1}{n-1}(PX)(PX)^T =\frac{1}{n-1}PXX^TP^T=PS_xP^T\end{equation}
由于$S_x$是对称矩阵，所以可以将$S_x$进行正交分解:
\begin{equation} \Lambda=E S_xE^T \end{equation}

我们可以想象一下，其实P和E是相等的。P的目的其实就是将$S_x$投影到几个主维度上去，而E的作用也是一样的。具体证明方法可以采用：  
由于$S_y$是投影后的矩阵，所有它的各个维度间的协方差基本为0。则： 
\begin{equation} S_y=\frac{1}{n-1}YY^T =\Lambda \end{equation}
所以在实际操作中，求出矩阵协方差后，直接进行正交分解，得到特征向量矩阵P。根据PX就可以得到降维后的矩阵。  

**PCA在matlab中使用**  
>[COEFF,SCORE,latent] = princomp(X)

COEFF是X矩阵所对应的协方差阵V的所有特征向量组成的矩阵，即变换矩阵或称投影矩阵。(principal component coefficients)   
SCORE是对主分的打分，也就是说原X矩阵在主成分空间的表示。( the principal component scores)  
LATENT协方差矩阵的特征值。(a vector containing the eigenvalues of the covariance matrix of X.)   
详见:[matlab官网][2]  
用原矩阵x\*coeff(:,1:n)就是新数据，其中的n是想降到多少维。具体要降到多少维度，需要看自己的需求，具体可以根据LATENT矩阵来判断
>percent = 100\*latent/sum(latent(:,1:n));

一般的话，当percent达到90%以上我们认为属于符合要求的。

  [1]: http://blog.csdn.net/ybdesire/article/details/6270328
  [2]: http://www.mathworks.cn/cn/help/stats/princomp.html
