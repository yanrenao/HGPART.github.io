# [Day 0] 每日精读论文——量化领域。

主要搜索了2018年以来CVPR、ICCV、ECCV、ICML、ICLR等顶会及期刊中关于量化的文章，希望在半个月内将其思想理解一遍，自勉。

## 一、经典文献（arxiv）

### 1. BinaryNet: Training Deep Neural Networks with Weights and Activations Constrained to +1 or -1.

论文链接：[BinaryNet](https://arxiv.org/abs/1602.02830v1)

**主要思想：**

> 把网络的权重和激活量化到2比特，并且通过随机梯度下降（SGD）来进行训练。

**方法：**

![img](https://pic1.zhimg.com/80/v2-b209552d8580de253bd36e67efd4e5c0_1440w.png?source=d16d100b)

2比特的量化方案，即符号函数。并且使用直通估计来进行计算梯度。

![img](https://pica.zhimg.com/80/v2-691dd12ee024add34b94edac03032945_1440w.png?source=d16d100b)

考虑到w没有设置边界（未做归一化），可能累计出太大的梯度，所以梯度在计算的时候设置其上下限以避免梯度爆炸。

![img](https://pic3.zhimg.com/80/v2-07f5e1d48ba648146c2b129395e012f7_1440w.png?source=d16d100b)



其中1|r|≤1可以用hard tanh来替代，其实就是截断函数。

**个人理解：**

> 低比特量化的开山之作。

### 2. Ternary Weight Networks

论文链接：[TWN](https://arxiv.org/abs/1605.04711)

**主要思想：**

> 提出三值化的网络，在二值化的基础上增加了零点。

**方法：**

![img](https://picx.zhimg.com/80/v2-197a44607570b6e56e5ba74938ebbd17_1440w.png?source=d16d100b)



三值化的量化方案。通过设定阈值Δ来确定量化的值。梯度计算用直通估计，并采用随机梯度下降。

![img](https://pic3.zhimg.com/80/v2-31339da8de0cb58af346df5be2bdf6f5_1440w.png?source=d16d100b)



量化网络的优化问题。

![img](https://pic2.zhimg.com/80/v2-11bffa5f3dc8afa37529361af901d07a_1440w.png?source=d16d100b)



优化问题进一步拆分。|IΔ|是量化成非零的数据个数，α是量化后的缩放系数，cΔ是与变量无关的常数。

![img](https://pic1.zhimg.com/80/v2-2eb944f2d0a9a435533f1b343adcd7f5_1440w.png?source=d16d100b)



α的最优解。

![img](https://pic3.zhimg.com/80/v2-5866d6251863eb01ff06800de1062d80_1440w.png?source=d16d100b)



α回代后，优化问题成单变量。但用离散的暴力求解耗时。

![img](https://pic2.zhimg.com/80/v2-9a0e5b2bbaf063dbe1295eaa070832b2_1440w.png?source=d16d100b)



若权重w成高斯分布，可以得到近似解。

**个人理解：**

> 这篇文章在二值化网络的基础上增加了零点，在保证模型极少的参数开销的同时，提高了精度。

### 3. DoReFa-Net: Training Low Bitwidth Convolutional Neural Networks with Low Bitwidth Gradients

论文链接：[DoReFa-Net](https://arxiv.org/abs/1606.06160)

**主要思想：**

> 相比于二值化和三值化的网络，这篇文章将量化拓展到任意比特位数（低比特），并且通过反向传播来计算缩放因子。

**方法：**

![img](https://picx.zhimg.com/80/v2-75a77c6c5ecc4fb21e51e2fdd93a5d4a_1440w.png?source=d16d100b)



二值数据点积运算的优化计算。

![img](https://pic4.zhimg.com/80/v2-b261ed6bb5cd5bc9c41426ab116b9672_1440w.png?source=d16d100b)



推广到低比特的二值数据的点积运算。

![img](https://pic2.zhimg.com/80/v2-a352a9cda4dd4a79c368828a18cccdac_1440w.png?source=d16d100b)



采用直通估计STE计算梯度。

![img](https://pic2.zhimg.com/80/v2-5711547ce37f28e5e6957ce6e8f44902_1440w.png?source=d16d100b)



权重的量化方案，采用的是用tanh来逼近截断操作。

![img](https://pic1.zhimg.com/80/v2-bbfb6462f437292a6652f32ba3787d62_1440w.png?source=d16d100b)



激活的量化方案。

![img](https://pic1.zhimg.com/80/v2-c09e3e2311a8fec55111ad1a5d98fa2b_1440w.png?source=d16d100b)



设计的用于反向传播的梯度计算。

![img](https://pic1.zhimg.com/80/v2-c2fc6df182705d63e0cc92da5a8bb17d_1440w.png?source=d16d100b)

在此基础上，还对梯度引入了噪声，来弥补量化的引入的误差。

![img](https://pic3.zhimg.com/80/v2-d3e3bb9ec0a3f7ebd25ab0bca5edecf5_1440w.png?source=d16d100b)

噪声的区间设计的与量化舍入的区间相同。

**个人理解：**

> 这是一篇很经典的论文，它的梯度设计很有意思。

## **二、CVPR 2018**

### 1. CLIP-Q: Deep Network Compression Learning by In-Parallel Pruning-Quantization

论文链接：[CVPR 2018 Open Access Repository](https://openaccess.thecvf.com/content_cvpr_2018/html/Tung_CLIP-Q_Deep_Network_CVPR_2018_paper.html)

**主要思想：**

> 将网络**剪枝**和权重**量化**结合在一个单一的学习框架中，同时并行进行权重剪枝和权重量化。

**方法：**

![img](https://pic1.zhimg.com/80/v2-1a301aef93de568846fd961d37ceb75b_1440w.png?source=d16d100b)

1. Clipping操作对数据进行修正，在两个红色箭头（记为c+和c-）之间的数据都被赋值为0，并且对被赋值为0的相关通路进行剪枝（操作可逆）。2. 对剩下的数据进行均匀分段（区间应该取的是最大值策略）。3. 计算每一个分段的平均值作为量化的放缩scale。

![img](https://pica.zhimg.com/80/v2-29f3f61eab8763e0c7a8663341ef50ac_1440w.png?source=d16d100b)

具体操作如上。

![img](https://pic2.zhimg.com/80/v2-cca466c87aa72956fc69055cbbdadd04_1440w.png?source=d16d100b)

用量化后的网络通过前向传播计算损失，反向传播用于直接调整全精度的网络（其实这里思想就有点接近直通估计STE了，是直接跳过量化器进行反向传播）。

> 剪枝这部分算法跳过。

**个人理解：**

> 这篇文章主要还是在对于模型轻量化不同方向的方法进行耦合，其量化算法本身没有太大创新。算法中量化区间的选取、量化的缩放系数scale取平均值的策略都有待商榷。不过对于c-和c+区间内的数据赋0的操作还是挺有意思的，c-和c+的取值如何进行设置或者学习，文章中似乎没有给明确算法。

### **2. ELQ: Explicit Loss-Error-Aware Quantization** for Deep Neural Networks

论文链接：[CVPR 2018 Open Access Repository](https://openaccess.thecvf.com/content_cvpr_2018/html/Zhou_Explicit_Loss-Error-Aware_Quantization_CVPR_2018_paper.html)

**主要思想：**

>  根据泰勒展式设计了一个新的损失，提出了一种**渐进量化**的训练方式。

**方法：**

![img](https://pic4.zhimg.com/80/v2-3c487c8e80411fecf56544233c098613_1440w.png?source=d16d100b)

添加图片注释，不超过 140 字（可选）

![img](https://pic1.zhimg.com/80/v2-afd416fd9c8c0518f46b88970a327d26_1440w.png?source=d16d100b)

添加图片注释，不超过 140 字（可选）

![img](https://pic2.zhimg.com/80/v2-9753fdbcc7765bcb582faa178bee644c_1440w.png?source=d16d100b)

如果全精度的数可以看作连续函数的话，量化的损失可以看作是全精度损失在若干离散点的泰勒展式。相比于牛顿公式（文章中有引用）需要求雅各比矩阵和海森矩阵（一阶导和二阶导），这种方法更加简单。

![img](https://pica.zhimg.com/80/v2-3872c8ee88afd2584d6bf4f88c642704_1440w.png?source=d16d100b)

scaling参数的计算公式。β是超参数，设置为0.05。

![img](https://pica.zhimg.com/80/v2-b9200cd389d7220f3bc63cb9e4c96794_1440w.png?source=d16d100b)

三比特量化方案。

![img](https://pic4.zhimg.com/80/v2-a370cb67a95ab6014995297a1f8ed55d_1440w.png?source=d16d100b)

二比特量化方案。

![img](https://pic2.zhimg.com/80/v2-3b41ddc76e49883cb461b86969543529_1440w.png?source=d16d100b)

整个量化过程是不断逼近的过程，并不是一步到位。通过设置多个阈值，每次迭代将阈值中的数进行量化，直到所有值都被量化。

**个人理解：**

> 这篇文章这种渐进式的量化思想是挺不错的，但是具体的方案还有待优化，不应该通过预先设置阈值来调整量化，这种方案适用性不强。此外，将量化作为连续信号的泰勒展式这种思想值得借鉴，如果说展式的阶数足够大，选取的量化点足够多或者足够好，量化损失就能够逼近全精度的损失。但是阶数和量化位数并不能够无限取大，所以需要优化得到足够好的量化点才是关键。

**3. Feature Quantization for Defending Against Distortion of Images**

论文链接：[CVPR 2018 Open Access Repository](https://openaccess.thecvf.com/content_cvpr_2018/html/Sun_Feature_Quantization_for_CVPR_2018_paper.html)

**主要思想：**

> 提出了用Floor函数以及幂函数来**改变特征（数据）分布**的方法，从而增加对图像噪声的容错，提高网络鲁棒性。

**方法：**

![img](https://pica.zhimg.com/80/v2-774b29241781b5a2b32f50cc6810cf08_1440w.png?source=d16d100b)

图像增加了扰动之后，数据分布会发生变化，而以往的归一化操作无法处理这种分布上的不同，引起网络较大的误差。

![img](https://pica.zhimg.com/80/v2-cd3c065cca2593a4a6920f25039ee2c8_1440w.png?source=d16d100b)

相比原始数据，使用归一化（Norm.）无法降低噪声带来的散度。用Floor函数、幂函数以及其他非线性操作可以改变数据的分布，进而降低散度。

![img](https://pica.zhimg.com/80/v2-adf3207d3f024bc2dc2f98ffb06e0ec5_1440w.png?source=d16d100b)

卷积操作。输入的数据X是c维度的，输出的结果U是d维度的。

![img](https://pica.zhimg.com/80/v2-7ff2ec1d95ab3ba220921ba49d736b14_1440w.png?source=d16d100b)

采用Floor函数后的卷积操作。

![img](https://pic1.zhimg.com/80/v2-d5b9f8b3c5e37fc720057bdace5ed7e2_1440w.png?source=d16d100b)

Floor函数是采用了量化的思想，先乘β后调整数据向下取整的区间，再除以β将数据恢复至原始区间。对输入x的梯度计算用的是STE直通估计。β是channel-wise的系数，不是通过反向传播学习的。论文中说这种Floor操作可以降低较小噪声的干扰。

![img](https://pic2.zhimg.com/80/v2-f41f522202f215e9bc4211c4c7e0efd1_1440w.png?source=d16d100b)

采用幂函数后的卷积操作。

![img](https://pica.zhimg.com/80/v2-84c7ef4d9234c186967673ed301b8955_1440w.png?source=d16d100b)

幂函数的设置，采用了对称设计。

![img](https://pic1.zhimg.com/80/v2-987fb91491f3c792d6e61fd0f9f5bcf0_1440w.png?source=d16d100b)

幂函数的指数以及输入的梯度计算。

**个人理解：**

> 这篇文章是从改变数据分布的角度来进行增加网络对于噪声的容错，这是一个很有意思的观点。但是网络如何权衡降低噪声的同时带来对数据非线性变化所产生的损失（例如，Floor函数在降低小噪声的同时引入了数据截断的噪声；幂函数等非线性变换所带来的数据分布是否是卷积所擅长的），论文还是做了比较初步的探索。此外，这篇论文从出发点上不属于通过量化降低网络复杂度的范畴，它是借用了Floor的这种量化操作来训练网络从而提高对噪声的容忍程度。不过这种非线性变换如果能很好的利用，应该能够在网络轻量化的量化领域降低量化所带来的误差。

### **4. IAO: Quantization and Training of Neural Networks for Efficient Integer-Arithmetic-Only Inference**

论文链接：[CVPR 2018 Open Access Repository](https://openaccess.thecvf.com/content_cvpr_2018/html/Jacob_Quantization_and_Training_CVPR_2018_paper.html)

**主要思想：**

> 将量化与硬件实现结合，把卷积操作中FP32的数据类型量化至硬件友好的Int**8比特**数据类型，并且在卷积计算后的偏置中用Int**32比特**来保证精度。提出了新的模拟量化训练的框架。

**方法：**

![img](https://pic3.zhimg.com/80/v2-a4eaf43986a0a2047e2b2cffc3093dfb_1440w.png?source=d16d100b)

图(a)：真实硬件中只有Int类型网络推理，在加法的过程中有数据截断的损失。图(b)：网络训练过程中模拟量化的框架。图(c)：在延迟确定的情况下（一定范围内），Int8比Float推理的精度更高。

![img](https://pic1.zhimg.com/80/v2-32af36112bfe9536120fad4848701a3e_1440w.png?source=d16d100b)

提出的全整型推理方案。Z是零点偏置，S是Scale系数，用于缩放。将量化后的整数域q通过仿射变换到实数域r。

![img](https://pic1.zhimg.com/80/v2-eb88cd67c8e305d7f1946aaf9ada3f61_1440w.png?source=d16d100b)

对于两个整型的矩阵相乘，可以做以上的化简。

![img](https://pic3.zhimg.com/80/v2-7454d33c247d601ef30d895aa8aca90e_1440w.png?source=d16d100b)

根据经验，发现M总是在区间(0,1)内，因此可以用标准化形式表示。M0为区间[0.5,1]内的Int8或者Int32，n为非负整数。这样所有的乘法运算全部都是硬件友好的整型乘法。

![img](https://pic3.zhimg.com/80/v2-4c6e733918c3ba77bea661ccf057a696_1440w.png?source=d16d100b)

通过将卷积操作进一步因式分解，可以降低硬件减法计算的复杂度。矩阵的主要复杂度还在矩阵乘法。

![img](https://pic1.zhimg.com/80/v2-443c83f655f645fdc9dce639ad8cc5c5_1440w.png?source=d16d100b)

提出的量化方案。对于clamp（clipping）的区间[a,b]选取策略，权重是用最大最小值（a:=max(w)，b:=min(w)），激活用的是EMA指数滑动平均来平滑处理。

![img](https://picx.zhimg.com/80/v2-e0b52710237ae33e53c4ef6e3ae05347_1440w.png?source=d16d100b)

最后将量化学习到的参数s和z进行映射，用于推理。

![img](https://pic1.zhimg.com/80/v2-c2e8ade849958b5df87944ed35d0f28e_1440w.png?source=d16d100b)

提出batch normalization folding的操作，用来替代batch normalization。原因是BN是在训练中对一个batch进行操作的，但推理是对一个数据进行操作的，会有效率上的损失。

**个人理解：**

> 这是一篇很经典的文章，所提出的量化和推理的框架到现在工业界都有在用。文章中考虑到硬件友好Int8和Int32精度来进行优化，很有应用价值。但实际的量化方案还有待改进，例如截断区间[a,b]的范围选取策略，缩放参数s和z是通过统计而非网络反向传播学到的。

### **5. Quantization of Fully Convolutional Networks for Accurate Biomedical Image Segmentation**

论文链接：[CVPR 2018 Open Access Repository](https://openaccess.thecvf.com/content_cvpr_2018/html/Xu_Quantization_of_Fully_CVPR_2018_paper.html)

**主要思想：**

> 将主动学习和量化进行结合，提高了图像分割的效果。

**方法：**

![img](https://pic1.zhimg.com/80/v2-a96a9a666a050a0ff08b8973d391a5e6_1440w.png?source=d16d100b)

前人（Suggestive Annotation: A Deep Active Learning Framework for Biomedical Image Segmentation）将主动学习应用于图像分割的框架。首先通过多个全卷积网络（FCN）来挑出具有代表性的不确定度高和相似度高的训练集样本，再用这些样本训练这些FCN，同时用这些样本训练最终的分割FCN，不断迭代。

![img](https://pic3.zhimg.com/80/v2-dfcfc8c96b45a2dc79db97706337cd1f_1440w.png?source=d16d100b)

前人认为不确定度对网络精度起到决定性作用，图(d)可以看出当不确定度越低，准确率越高。但是以往地FCN的结构存在很大的冗余，虽然用不同的FCN进行选取不确定度高的样本，最终的结果(b)中可以看出绝大多数的分割效果都是非黑即白的，处于灰色地带的地方比较少，也就是说网络比较自信（容易过拟合）。

![img](https://pic1.zhimg.com/80/v2-ab41721c950c8d4c07188c3ea384586c_1440w.png?source=d16d100b)

本文在以上的基础上对所有FCN都增加了量化操作。其中量化方案用的是前人的方法（Incremental Quantization (INQ)、DoReFa-Net和Ternary Weight Networks (TWN)）。

![img](https://picx.zhimg.com/80/v2-5d03bae9d7be90d7e477dd4712c70614_1440w.png?source=d16d100b)

文章发现增加量化操作后，网络对于边界的输出准确度没有下降，并且提高了网络的不确定度。这有利于选出具有代表性的样本。

**个人理解：**

> 这篇文章从理论上没有创新，就是将量化误差引入到主动学习中不确定度的衡量中。所采用的量化方案也是基于前人的方法。相比之下，我觉得前人的方案更具有启发性。这篇文章更像是做一个拼接上的应用。

### **6. SYQ: Learning Symmetric Quantization for Efficient Deep Neural Networks**

论文链接：[CVPR 2018 Open Access Repository](https://openaccess.thecvf.com/content_cvpr_2018/html/Faraone_SYQ_Learning_Symmetric_CVPR_2018_paper.html)

**主要思想：**

> 对于低比特量化，通过增加权重量化过程中的缩放因子的细粒度，来提高模型量化后的表达能力。

**方法：**

![img](https://pica.zhimg.com/80/v2-541923eae95f943d4d42655935418eb9_1440w.png?source=d16d100b)

首先，对于权重的量化。用一个对角矩阵（K×K大小，K为该层特征的长宽）来代替之前量化所用的缩放系数s以增加细粒度。因为是对角矩阵，非对角线的元素均为零，所以存储时可以用向量的方式存储以降低存储尺寸。

![img](https://pic2.zhimg.com/80/v2-cefac36106e0e8384b4e59dc5256d020_1440w.png?source=d16d100b)

对于每一个对角线元素的梯度计算就是对权重求偏导。

![img](https://picx.zhimg.com/80/v2-f8aee5e774e795d08ae0e10cbde0f366_1440w.png?source=d16d100b)

采用二比特或者三比特的量化方案，并且使用直通估计，上述梯度计算公式可以进一步更新。

![img](https://pic1.zhimg.com/80/v2-c9b74aa48b9a0633be13ef2ea32b1383_1440w.png?source=d16d100b)

用权重的均值来初始化α。

![img](https://pic1.zhimg.com/80/v2-7a9c69e348bd8d496abb01c45088cd4f_1440w.png?source=d16d100b)

其次，对于激活的量化，用的是2的指数的非均匀量化方案。

![img](https://pic1.zhimg.com/80/v2-bcdc169f0ed1359b7b48338b4ba9a640_1440w.png?source=d16d100b)

反向传播用的是直通估计。

![img](https://pic1.zhimg.com/80/v2-ea852e1923febc65ba48ddc01b30da96_1440w.png?source=d16d100b)

pixel-wise卷积的方案。卷积核输入是I维，输出是N维。

![img](https://pic2.zhimg.com/80/v2-456f7d4659a467098ff6afce0fa85f8e_1440w.png?source=d16d100b)

row-wise卷积的方案。相比与pixel-wise卷积的方案，细粒度降低。但比普通的layer-wise卷积的方案，细粒度还是提高的。

![img](https://pica.zhimg.com/80/v2-afdede69ce8cb8c445895e6acf8d68eb_1440w.png?source=d16d100b)

对比其他方法，这种row-wise和pixel-wise的量化后精度下降要更小，并且额外所需的存储量以及操作数都是可接受的。

**个人理解：**

> 这篇文章通过一个缩放的系数矩阵来进一步提高量化的细粒度，在低比特量化中的开销和训练时间是可接受的。这个方法我觉得还是可行的，不知道在高比特量化的时候会有什么问题。

### 7. Two-Step Quantization for Low-Bit Neural Networks

论文链接：[CVPR 2018 Open Access Repository](https://openaccess.thecvf.com/content_cvpr_2018/html/Wang_Two-Step_Quantization_for_CVPR_2018_paper.html)

**主要思想：**

> 将权重量化和激活量化分开训练，从而避免直通估计产生的梯度误差所引起的不收敛问题。

**方法：**

![img](https://pica.zhimg.com/80/v2-43e2587d0a288e4712d8c11a689513f3_1440w.png?source=d16d100b)

以往的均匀量化的方案。如何选取量化区间和量化间隔是关键。

![img](https://pica.zhimg.com/80/v2-ec955d7068f0750c7fa16651d896ab25_1440w.png?source=d16d100b)

Half-Wave Gaussian Quantizer (HWGQ)方案是先将权重归一化后通过Lloyd算法进行优化。

![img](https://pica.zhimg.com/80/v2-069c195f3a32f60a9e148de4ec42a148_1440w.png?source=d16d100b)

首先，对激活的量化方案进行训练（code learning）。所提出的稀疏量化是基于大激活比小激活更重要的假设，把低于阈值ε的数都量化到0。

![img](https://pic2.zhimg.com/80/v2-9c8a6608b4197ddc7baacf99b0a023d7_1440w.png?source=d16d100b)

再通过Lloyd算法进行优化量化区间和量化间隔。

![img](https://pic2.zhimg.com/80/v2-3dbec9ebd573ba61144db47ecfeda432_1440w.png?source=d16d100b)

而稀疏阈值ε的选取是通过对数据先进行batch normalization后，根据预先设定的稀疏比系数θ来确定的。

![img](https://pic4.zhimg.com/80/v2-1bf4232a9cd260d80f2f40070c9bc7c4_1440w.png?source=d16d100b)

不同稀疏比下的阈值以及量化间隔。

![img](https://pica.zhimg.com/80/v2-befaa47fda66da1c351cbfc14fec2474_1440w.png?source=d16d100b)

其次，对权重的量化方案进行训练（transformation function learning）。在激活的量化方案确定后，X和Y的量化损失被忽略。从而变成求解非线性最小二乘回归问题。其中α是row-wise的缩放系数。

![img](https://pic1.zhimg.com/80/v2-a58d967386b231f923aa7853844cf7c2_1440w.png?source=d16d100b)

对于该问题，可以分解成求对于每个row的最小损失的子问题。

![img](https://picx.zhimg.com/80/v2-d7ce96e889c1c63c28019c4d615f43fd_1440w.png?source=d16d100b)

再引入中间变量z，把上述的子问题进一步分解成：1）权重量化后的矩阵相乘的结果逼近实数z，2）若干实数z在量化后的分布逼近输出y。如果1）结果收敛，那么这一项是无穷小，只需要优化2）即可。

![img](https://pica.zhimg.com/80/v2-69a1c37138172fd92c7a739b3a8d0745_1440w.png?source=d16d100b)

当z固定时，原始损失就剩下后一项，对其做展开后，可以通过梯度为0来确定缩放系数α的值。再把确定的α*回代，可以得到权重w的取值公式。w是一个m维的向量，每个数有n比特的取值范围。每次迭代只优化一个数而固定剩下的数来逼近权重w的最优解。

![img](https://pica.zhimg.com/80/v2-3a16b0e238ae504b2dd151918dd7b03f_1440w.png?source=d16d100b)

当α和w固定时，优化问题变成两个一维问题。通过把量化函数的约束进行放松，可以得到z在不同区间的解。

> 对于非对称量化的优化问题也是大同小异。

**个人理解：**

> 这篇文章是对于渐进式的量化做了探索，这种two step的方法其实是一种贪心算法，能不能达到全局最优还是存疑，不过也是很好的尝试。此外，这篇文章摒弃了round-to-nearest这种想法，通过优化问题的分解直接对权重进行映射，是PTQ方案中比较独特的想法。但其优化策略也说明是只能对于低比特量化进行的，否则训练时间太久甚至难以收敛。