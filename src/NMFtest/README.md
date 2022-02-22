# 非负矩阵分解(NMF,Non-negative matrix factorization)

## NMF的发展及原理
　　
著名的科学杂志《Nature》于1999年刊登了两位科学家D.D.Lee和H.S.Seung对数学中非负矩阵研究的突出成果。该文提出了一种新的矩阵分解思想——非负矩阵分解(Non-negative Matrix Factorization，NMF)算法，即NMF是在矩阵中所有元素均为非负数约束条件之下的矩阵分解方法。

该论文的发表迅速引起了各个领域中的科学研究人员的重视：一方面，科学研究中的很多大规模数据的分析方法需要通过矩阵形式进行有效处理，而NMF思想则为人类处理大规模数据提供了一种新的途径；另一方面，NMF分解算法相较于传统的一些算法而言，具有实现上的简便性、分解形式和分解结果上的可解释性，以及占用存储空间少等诸多优点。为高效处理这些通过矩阵存放的数据，一个关键的必要步骤便是对矩阵进行分解操作。

通过矩阵分解，一方面将描述问题的矩阵的维数进行削减，另一方面也可以对大量的数据进行压缩和概括。
　　
利用矩阵分解来解决实际问题的分析方法很多，如PCA(主成分分析)、ICA(独立成分分析)、SVD(奇异值分解)、VQ(矢量量化)等。在所有这些方法中，原始的大矩阵V被近似分解为低秩的$$V=WH$$形式。这些方法的共同特点是，因子W和H中的元素可为正或负，即使输入的初始矩阵元素是全正的，传统的秩削减算法也不能保证原始数据的非负性。在数学上，从计算的观点看，分解结果中存在负值是正确的，但负值元素在实际问题中往往是没有意义的。例如图像数据中不可能有负值的像素点；在文档统计中，负值也是无法解释的。

## NMF的基本思想
　　
NMF的基本思想可以简单描述为：对于任意给定的一个非负矩阵$$\mathbf{A}$$，NMF算法能够寻找到一个非负矩阵$$\mathbf{U}$$和一个非负矩阵$$\mathbf{V}$$，使得满足$$\mathbf{A}=\mathbf{U}\times\mathbf{V}$$，从而将一个非负的矩阵分解为左右两个非负矩阵的乘积。



分解前后可理解为：原始矩阵的列向量是对左矩阵中所有列向量的加权和，而权重系数就是右矩阵对应列向量的元素，故称为基矩阵，为系数矩阵。一般情况下的选择要比小，即满足，这时用系数矩阵代替原始矩阵，就可以实现对原始矩阵进行降维，得到数据特征的降维矩阵，从而减少存储空间，减少计算机资源。

由于分解前后的矩阵中仅包含非负的元素，因此，原矩阵A中的一列向量可以解释为对左矩阵U中所有列向量(称为基向量)的加权和，而权重系数为右矩阵V中对应列向量中的元素。这种基于基向量组合的表示形式具有很直观的语义解释，它反映了人类思维中“局部构成整体”的概念。研究指出，非负矩阵分解是个NP问题，可以划为优化问题用迭代方法交替求解U和V。NMF算法提供了基于简单迭代的求解U，V的方法，求解方法具有收敛速度快、左右非负矩阵存储空间小的特点，它能将高维的数据矩阵降维处理，适合处理大规模数据。利用NMF进行文本、图像大规模数据的分析方法，较传统的处理算法速度更快、更便捷。

由于NMF不允许基图像或中间的权重矩阵中出现负值，因此只有相加组合得到的正确基图像才允许，最后通过处理后的重构图像效果是比较满意的(对矩阵非负的限制使得这种分解能够达到用部分表达整体的效果,简单地说就是,整体由部分的叠加而没有了正负抵消 )。[Learning the parts of objects by non-negative matrix factorization]

## 非负矩阵分解NMF的一个示例解释

![](https://pic3.zhimg.com/56079895270388df370b8643f3537dee_r.jpg)

在VQ分解中，每一列的被约束为一个一元矢量。其中只有一个元素为1，其他元素为0。若的第一列中，第$$r_1$$个元素为1，那么中第一列的脸，就完全由基图像中的第$$r_1$$列数据表示。此时得到的基图像称为原型基图像，这些原型图像表示一张原型脸。

在PCA分解中，的各列之间相互正交，各行之间相互正交。这个约束比VQ的松弛很多，也就是，中的元素可为正也可为负。中每一张脸的每一个像素点都是中各列对应的像素点的一个加权和。由于权重矩阵中元素符号的任意性，所以基矩阵表示出来并不像VQ中原型脸那样的直观可解释。此时将W的列数据画出来并不一定能直接看到一张“脸”。但是在统计上可以解释为最大方差方向，我们把这些“脸”称为“特征脸”。

在NMF中，由于加了非负约束。与VQ的单一元素不为0不同，NMF允许基图像H间的加权结合来表示脸部图像V；与PCA不同，NMF的加权系数H中的元素都为非负的。前两者得到的都是一个完整的脸部特征基图像，而NMF得到的是脸部子特征。通俗点说，VQ是用一张完整的图像直接代表源脸部图像；PCA是将几个完整人脸加减压成一张脸；而NMF是取甲的眼睛，乙的鼻子，丙的嘴巴直接拼成一张脸。这样解释虽然细节上略有不妥，但不失其概念上的意义。

通过图1中的面部特征提取例子可领略NMF处理数据的方式。最左边的大矩阵由一系列的小图组成，这些小图是分析数据库中包含的2429个脸部图像的结果，每幅图像由19×19个像素组成。传统方法中这样的小图是一幅完整的人脸图像，但是在NMF方法中，每个小图是通过一组基图像乘以一个权重矩阵而产生的面部特征图，经过这样处理的每幅小图像恰好表示了诸如“鼻子”、“嘴巴”、“眼睛”等人脸局部概念特征，这便大大压缩了存放的图像数据量。左边的大矩阵由每幅小图像的19列一起组成矩阵的一列，那样它就是19×19=361行，2429列。这个例子中，NMF方法用基图像来代表眼、眉毛、鼻子、嘴、耳朵、胡子等，它们一起组成了数据库中的脸。这样给人最先的直觉就是它很好地压缩了数据。事实上Lee和Seung在他们的论文中更深入地指出，与人类识别事物的过程相似，NMF也是一种优化的机制，近似于我们的脑分析和存储人脸数据的过程。这个例子中，原图像表示这些局部特征的加权组合，这与人类思维中“局部构成整体”的概念是相吻合的。因此，NMF算法似乎体现了一种智能行为。