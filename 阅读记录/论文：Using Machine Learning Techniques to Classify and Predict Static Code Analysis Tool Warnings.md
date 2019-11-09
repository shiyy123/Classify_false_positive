# Using Machine Learning Techniques to Classify and Predict Static Code Analysis Tool Warnings

《使用机器学习技术对静态代码分析工具警告进行分类和预测》

本文使用软件工程指标（即源代码指标）将【由静态代码分析（SCA）工具生成的错误消息】分类为真阳性，假阳性或假阴性的工作。在8个数据集中比较了SVM、KNN、随机森林、RIPPER这四种算法的表现能力。研究结果显示，复杂指标和耦合指标对SCA工具是否误报有重要影响。

用Understand工具生成源代码的软件工程指标。



## 实验过程

分为两个阶段：

### A.数据生成阶段

第一阶段，用两种方式分析已有的源代码，生成数据集。这一阶段分为两个小步骤，第一是计算软件工程指标（比如大小、复杂度）；第二是用SCATE（Static Code Analysis Tool Evaluator）框架分析SCA工具的警告。

1）分析SCA工具的警告

SCATE框架，基于TP、FP、FN来计算SCA工具的质量。

2）计算软件工程指标

借助Understand。

该阶段的输出是一个数值数据集，它对应于数据矩阵的内容，其中矩阵的每一列代表一个特定的软件工程度量。但是，最后一列代表一个类值（即，TP（用1表示），FP（用2表示）和FN（用3表示））。同样，矩阵中的每一行对应于源代码中的给定函数。在某些特殊情况下，当SCA工具仅生成两种类型的标签或类时，生成的数据集将是二进制数据集。



### B.学习阶段

这一阶段的主要目的是用一系列ML技术，针对第一阶段生成的数据集，构造一个分类器。

另外一个目的就是检验哪种软件工程指标对SCA工具的TP、FP、FN判别影响最显著。

这一阶段又包含三个阶段。

1）预处理。

SCA工具可以针对同一个函数生成多个警告（包含FP、FN、TP），这可能导致生成的数据集中的数据点矛盾。为了解决这一问题，需要对特征值进行归一化（normalization），并且用 Correlation-based Feature Selection技术进行特征选取。

2）模型学习。实验了4种模型：SVM、KNN、随机森林、RIPPER。

3）预测。预测一段未知的源码中的函数是否会导致SCA工具生成TP、FP、FN。



### 其他

#### A. SCA工具：

- Tool1：开源的SCA工具，针对C++源代码
- Tool2：商业SCA工具，针对C++和Java，可以分析源代码和二进制。



#### B. 测试用例（源代码）

测试用例选自National Security Agency’s (NSA) Center for Assured Software (CAS)



#### C. 缺陷

- CWE-252: Unchecked Return Value.（未检查的返回值）
- CWE-369: Divide by Zero.（除以0）
- CWE-415: Double Free.（对同样的内存地址两次使用free()函数）
- CWE-457: Use of Uninitialized Variable.（使用没有初始化的变量）
- CWE-426: Untrusted Search Path.（不信任的搜索地址）
- CWE-762: Mismatched Memory Management Routines.（返回一个已经被释放了的函数）
- CWE-476: NULL Pointer Dereference.（空指针间接引用)



### 关于机器学习技术

#### 特征提取

使用Correlation-based Feature Selection (CFS)进行特征提取。

下表列出了在将CFS应用于每个数据集后确定的相关软件工程指标。

![gdrMuu.png](https://t1.picb.cc/uploads/2019/11/08/gdrMuu.png)



#### SMOTE过采样

SCA工具会生成大量的FP和FN警告，从而导致生成的数据集中,TP,FP,FN的数量不成比例，这种问题被称为数据不均衡的问题。为了解决这种问题，提高分类器的能力，使用SMOTE技术（Synthetic Minority Over-sampling Technique）来平衡训练和测试数据集。SMOTE可能会导致过拟合的问题，使用交叉验证来解决过拟合。

#### 分类技术

四种分类技术： instance-based learning（基于实例的学习，KNN）, ensemble learning（整体学习,RF）, rule-based learning（基于规则的学习,RIPPR）, and statistical learning（统计学习）



## 实验结论

模型的效果使用 F-measure进行评分，F-measure根据精度和召回率之间的平衡选择最佳模型，作为满足我们比较需求的性能指标。实验表明随机森林（RF）的效果最好。原因之一是，随机森林技术在大型数据集（例如CWE415-Tool2）上的效果特别好；另一个原因是CFS选择了软件工程指标的最佳子集，并将其传递给Random Forests。

![gdr8Ya.png](https://t1.picb.cc/uploads/2019/11/08/gdr8Ya.png)

1. 随机森林的效果最好，RIPPER其次。
2. 输入计数、节点数、输出计数、路径计数、圈复杂度、基本复杂性，这些软件工程指标对SCA工具分析的准确性有重要影响。
3. 如果开发者重新写相关源代码，减少源代码的复杂性、降低耦合度、减少全局变量的使用，FP和FN的数量可以减少。
4. 实现高性能的ML技术可以应用于具有相同性质和结构的新的和不同的测试用例。

未来工作：

1. 将此方法应用于更多种开源/商业的软件工程中。
2. 对于源代码，以行为单位计算软件工程指标，以减少数据集中的数据点矛盾问题。
3. 扩展运用更多ML算法和更多SCA工具。