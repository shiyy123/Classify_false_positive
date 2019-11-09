# Using Software Engineering Metrics to Evaluate the Quality of Static Code Analysis Tools

《使用软件工程指标评估静态代码分析工具的质量》

本文提出了一个框架，用于在不同软件工程指标的背景下评估静态代码分析（SCA）工具的质量。本文实验不涉及ML相关算法。

## 背景知识

### Metric：软件工程指标

 这篇论文使用“Understand”（https://scitools.com/）工具来生成软件工程指标。 以下是几大类指标的解释：

- **Basic Count Line Metrics**（基本计数行指标）：这些指标检索函数、类或文件范围内源代码中每一行的信息。

- **Basic Count Metrics**（基本计数指标）：两类，一类是统计文件中有多少类；另一类是统计文件中有多少函数。

- **Basic Token Metrics**（基本标识符指标）：代表源代码的复杂性。

- **Control Flow Metrics**（控制流指标）：由函数控制流图计算得出。其中有Knots（节点数）。

- **Miscellaneous**（杂）：分为“计算输入”和“计算输出”两种指标，这些指标在项目层面、文件层面、函数层面计算，本论文只考虑文件层次和函数层次。

  Fan-In（扇入）：表示一个模块被多个模块调用。

  Fan-Out（扇出）：表示一个模块调用多个模块。

  此度量标准对开发人员很重要，因为它可以帮助开发人员了解解决一个功能缺陷对其他功能的负面影响。

### SCA工具的错误信息类型

- True positive（TP）：真阳性，发现了预先定义的缺陷
- False positive（FP）：假阳性，误报
- False Negative（FN）：假阴性，漏报

### 实验结果着重关注的几种CWEs

- CWE-369：Divid by zero（除以0）
- CWE-415：Double Free（对同样的内存地址两次使用free()函数）
- CWE-457：Use of uninitiated variable（使用没有初始化的变量）
- CWE-762：Mismatched memory management routines（返回一个已经被释放了的函数）

### SWAMP

The SoftWare Assurance MarketPlace (SWAMP)是一个云环境，用于针对不同的静态代码分析工具运行源代码。SWAMP提供19个开源SCA工具和4个商业SCA工具，支持5种编程语言：C/C++，Java，Python，Ruby。



## 实验过程

1. 使用import命令来解析Juliet Test套件中的源文件，并建立具有基本事实的知识库。 基本事实包含有关缺陷的信息，例如在测试用例中标记的函数名称和行号。 在此步骤中，框架将解析大约61,000个C ++或Java文件，具体取决于我们针对评估的SCA工具。
2. 针对测试套件中的源代码，在SWAMP中本地或远程运行SCA工具，然后捕获SCA工具生成的输出，因为我们需要这个来评估SCA工具是否正确地标记了源码中的缺陷。
3. 在Understand中运行测试用例相关的源码，来评定每个CWE的软件工程指标的值。
4. 将基本事实与SCA工具生成的输出进行比较，并针对不同的软件工程指标评估了SCA工具的性能。

共针对5种SAC工具进行了实验：

- Tool1：开源，支持C++
- Tool2、Tool3：商业，支持C++
- Tool4、Tool5：开源，支持Java



### 实验结果

- 基本复杂性-漏报， （CWE-762） 

![gdrOvD.png](https://t1.picb.cc/uploads/2019/11/08/gdrOvD.png)



- 输出计数-真阳性，发现预知缺陷（CWE-369）

![gdrWUv.png](https://t1.picb.cc/uploads/2019/11/08/gdrWUv.png)



- 圈复杂性-真阳性，发现预知缺陷（CWE-369）

![gdr76i.png](https://t1.picb.cc/uploads/2019/11/08/gdr76i.png)



- 节点数-真阳性，发现预知缺陷（CWE-369）

![gdrhML.png](https://t1.picb.cc/uploads/2019/11/08/gdrhML.png)



- 输入数量-真阳性，发现预知缺陷（CWE-457）

![gdrvPW.png](https://t1.picb.cc/uploads/2019/11/08/gdrvPW.png)



- 路径日志数量-真阳性，发现预知缺陷（CWE-369）

![gdrNgw.png](https://t1.picb.cc/uploads/2019/11/08/gdrNgw.png)

### 结论&未来计划

1. 源代码的复杂性高时，每一种SCA工具的正确率都比较低，这是因为源代码会更加复杂、易于出错、难以理解。导致SCA工具漏报的软件工程度量的第二种类型是面向对象的度量，例如CountOutput。例如，当源代码的函数之间耦合性较高时，SCA工具发现的缺陷会减少。volume指标对SCA工具质量的负面影响最小。
2. 对于给定的源代码，选择一个SCA工具可以依靠几个重要特征，比如开发者想要测试的漏洞类型，软代码的指标和结构。例如Tool2不适合检测有很多节点数量的源码，但比较适合高耦合的源码。
3. 实验结果表明，SCA工具发现的缺陷数量与软件工程指标的值之间存在关联。因此，我们计划在各种软件工程指标的背景下，使用多种机器学习技术预测给定源代码上SCA工具的性能（即生成TP，FP或FN的能力）。
4. 已经评估了五个同时支持C ++和Java编程语言的SCA工具以及它的映射到CWE的规则。将来，我们计划将这项研究扩展到涵盖更多可分析Python，Ruby，C ++和Java源代码的开源SCA工具。
5. SCA工具的误报率比较高，人工检测不可避免，而且耗费大量的时间和成本。我们计划通过提高分析的准确性或在生成错误警告后对错误警告进行后处理，来扩展提议框架的功能以解决此问题。