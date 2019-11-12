## Learning a Classifier for Prediction of Maintainability based on Static Aanlysis Tools

*个人觉得这篇作用不大*

通过与专业质量分析人士合作，我们手动检查了3个不同（Java）项目的源代码，共计115000+行，345个文件，同时评估它们的可维护性（maintainability）。然后我们训练出一个机器学习算法，基于代码度量（code metric）预测程序的可维护性。

其中代码度量包括：

- 结构性度量，如嵌套深度（nesting depth）
- 克隆信息
- 抽象信息，如code smell的数量

#### 实验

与其他实验不同，我们不使用任务完成时间来代表可理解性，也不使用修正的数量来代表可维护性；而是使用工业界专家的经验来定义可维护性。我们将可维护性定义为“变化的难易性”（ease of change），包括以下两个特征：

- 作为开发者，能否理解代码的功能，并且识别出实现了哪方面功能？
- 作为开发者，当修改代码时，是否需要考虑相关依赖的代码修改？

实验的总体流程入下图：

<img src="D:\ISELAB\毕设\学习模块\论文\Classify_false_positive\阅读记录\相关图片\image-20191112172013556.png" alt="image-20191112172013556" style="zoom:50%;" />

工具提取出的一些属性示例：总共67个属性；通过特征选择选出合适的几个特征，进行分类器的训练

- Size：
  - 代码行数
  - 代码的Source行数（Source Lines of Code）
  - 代码的方法行数
  - 条件的数量
- Structural：
  - 最大方法长度
  - 方法的平均长度
  - Block的最大深度
  - Loop的长度
  - Loop的最大深度
- Cloning：
  - 克隆范围
  - 克隆Units
- Complex Measurements：
  - 认知复杂度
  - Code Smell
  - 由Teamscale识别的质量违规数量

人工审核出3个标签：

- Label A：缺少修改难以程度判断方面的相关指标
- Label B：有提高空间
- Label C：难以维护的































