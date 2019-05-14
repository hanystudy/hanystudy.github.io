---
layout: post
title: "软件设计与架构笔记(13)"
date: 2019-05-14 11:14:51 -0400
comments: true
categories: 
- architecture
tags:
- software design
- software architecture
- design diagnosis
---

## 设计诊断

**设计诊断**(Design diagnosis)是指获取、分析、检测软件设计及其质量的活动。经济利益是驱动软件设计诊断的重要原因之一，特别是对于具有重要价值的软件设施来说，尽早发现并解决设计中存在的问题是十分必要的。然而一直以来软件设计都被认为是难以度量和评价。一方面是因为设计中包含的决策通常是涉及对领域、技术或其它上下文因素的权衡，这是任何客观标准都难以做到完全覆盖的，因此始终无法100%消除对主观参与的依赖，导致设计诊断的权威性受到限制。另一方面，已有的设计验证方法普遍落后于软件开发技术的创造和更替。尽管[设计原则与代码味道](http://www.hanyi.name/blog/2019/04/23/software-design-and-architecture-notes-12/)具有一定的普适性，但是大部分原则本身缺少明确的定量或定性规则(即使存在这类规则一般也很难适用于新的开发技术)，少量有明确的规则通常被归为静态代码分析和风格检查，尚不足以达到设计诊断的目的。

因此为了实现设计诊断，一方面需要对软件设计进行统一的形式化表示，避免对具体的软件开发技术产生依赖。在前者的基础上进一步分析当前软件设计，帮助分析人员理解现有设计、发现设计中的潜在缺陷、甚至模拟历史和未来的设计演化，从而为工程进度和技术债管理提供依据。本文的剩余部分将详细讨论这些主题。

### 设计分析(Design analysis)

设计分析的目标是研究设计本身，后者通常表示解决问题的方案，也可以指构建解决方案的过程。[BC00]认为，理想的解决方案可被视作具有一系列特性的集合，其中的每种特性都可以被归纳为某个维度，即**设计参数**(Design parameters)。不同的设计参数之间存在一定的依赖关系，即**设计结构**(Design structure)。设计参数的所有可能值的集合被称作**设计空间**(Design space)。在整个设计过程中，每个设计参数由对应的**设计任务**(Design tasks)决定，后者相互之间的依赖关系被称为**任务结构**(Task structure)。例如要设计一个马克杯，“是否包含杯盖”是一个设计参数，而“杯盖直径”则是依赖于前者的另一个设计参数，而对于“容器直径”来说，“杯盖直径”与其存在相互依赖的关系。相应地，分别负责杯盖和容器的设计任务之间也就存在依赖，因此设计结构和任务结构具有一致性。前述这些结构可以用一种邻接方阵进行表示，即接下来要讨论的**设计/任务结构矩阵**(Design/Task structure matrix，DSM/TSM)。

#### 设计结构矩阵

在DSM(TSM)中，每个结点表示设计参数(或设计任务)，两个设计参数之间的依赖用符号x进行表示，一个马克杯的设计结构例子如下图所示:

![Full DSM](http://content.hanyi.name/images/design_diagnosis/full_dsm.png)

虽然该DSM只有10x10，但每个结点间依赖都可能包含了丰富的物理和工程属性，真实场景中也许会非常复杂。根据设计参数之间的依赖关系类型，在DSM中可以进一步发掘出一些微结构，例如:

![Micro DSM](http://content.hanyi.name/images/design_diagnosis/dsm.png)

上图描述了两种微观的设计结构，(a)表示层次结构，(b)表示无层次的相互依赖结构，这两种设计结构实质上体现了不同的关系强度，显然(b)体现了更强的相互关系。

另外，真实案例中的矩阵规模通常要比马克杯大得多，例如一个设计笔记本电脑的TSM如下图所示:

![A laptop computer TSM](http://content.hanyi.name/images/design_diagnosis/computer_tsm.png)

上图中的TSM相比于马克杯的例子有几个新的元素。首先矩阵中的设计任务呈现出特定排序(或呈现为下三角矩阵)，即相互之间存在强关联的设计任务被放置的更近，越接近对角线的依赖密度就越高。其次整个矩阵上产生了若干相互关联的区块，这些区块直接反映了系统中的独立组件，例如驱动系统、主板等，被称作**原型模块**(Protomodules)。原型模块通常是由领域知识或者组织结构等上下文决定，但是并非真正意义上的模块，因为其本身不具有接下来要讨论的**模块性**(Modularity)。

#### 模块性与设计规则

一般而言，高复杂度的问题会导致同样高复杂度的设计，而一个“好”的设计能够有效地管理其自身复杂性。[BC00]认为，**模块化**(Modularization)是系统管理自身复杂性的核心，也是二十世纪以来计算机乃至更多其它领域得以飞速发展的重要原因。系统的模块化程度体现为**模块性**(Modularity)，其中包含两个重要概念:

**模块**，即内部元素间的关系比与外部元素间更强的系统单元，这些关系的相对强弱决定了模块的粒度。

**抽象**、**信息隐藏**和**接口**，即当一个系统达到一定复杂度时，需要将其拆分成不同部分，抽象的目的在于隐藏其内部复杂性，且通过接口与系统的其它部分进行交互。

在设计笔记本电脑的TSM中，我们知道主板和显示屏之间的设计任务多存在循环依赖，一个例子如下图所示:

![Cycling in a laptop computer TSM](http://content.hanyi.name/images/design_diagnosis/design_rule_tsm.png)

该例中的多个设计任务因为相应设计参数而存在互相依赖，例如主板要决定CPU的规格和所采用的中断协议，而显示屏需要确定详细规格。当主板中具有独立的图形控制器时，显示屏的规格就会发生改变。否则CPU就要根据显示屏的规格提供更多的计算能力，并且采用不同的中断协议。由此可见，“主板是否包含独立的图形控制器”就成为其它设计参数的关键依赖参数。从系统的角度看，其整体复杂性通常是由一系列关键依赖参数决定的，一旦其中某个设计参数确定，则许多依赖的设计参数也就相应确定。这些关键依赖参数被称作**设计规则**(Design rule)。一个完整的系统设计规则集合应至少包含如下信息:

* 模块及其在系统中扮演的角色。

* 模块间通信的接口。

* 系统集成协议以及测试某个模块是否遵循设计规则。

通过抽取设计规则可以消除原型模块间的相互依赖，从而形成真正意义的模块。其中，设计规则被称为**显性模块**(Explicit modules)，而其他相互独立的部分被称为**隐性模块**(Implicit modules)。一个模块化后拥有完整设计规则集合的DSM/TSM所下图所示:

![Modularization](http://content.hanyi.name/images/design_diagnosis/modularization.png)

DSM/TSM对于计划设计过程同样具有意义，在上例中，首先进行的是设计规则阶段，然后进入可并行进行的隐性模块设计阶段，最后是系统集成和测试阶段。其中，设计规则作为所有阶段的输入，隐性模块则作为集成和测试阶段的输入。

#### 模块演化及其模拟

模块性反映了系统的结构状况。如果一个系统具有嵌套层级结构，每个结构单元对内强关联，对外则相互独立，并且具有良好的功能角色定义——那么该系统就被称作**模块化系统**。值得注意的是，系统结构并非一成不变，一方面是因为某些设计参数间的依赖并不容易在初期就显现出来，另一方面，由于复杂的结构往往导致更高的经济成本，因此在真实场景中更加倾向于寻求结构和经济之间的平衡。为了描述模块的动态特征，可以采用**模块操作符**(Modular operators)，[BC00]提出了六种最基本的模块操作符，后者能够用于表示动态结构的所有可能演化路径:

* **分解**(Splitting)，把现有设计或任务划分成多个模块，在层次结构中这往往意味着产生了新层，例如以下模块化层级设计:

![Two-level modular design hierarchy](http://content.hanyi.name/images/design_diagnosis/dsm_splitting_before.png)

上例中描述了A~D四个隐性模块以及一个集成和测试阶段，从模块化的角度来看它们都属于相同层级。当更多设计参数及其依赖显现，并且上下文满足模块化设计需求时，新的设计规则以及相应的接口、测试就会出现，于是就诞生了新的层级，如下图所示:

![Three-level modular design hierarchy](http://content.hanyi.name/images/design_diagnosis/dsm_splitting_after.png)

在进行分解操作后，新的层级应当只对其所依赖的设计规则负责，而对全局设计规则以及上层的集成和测试部分保持透明，这对设计任务和阶段执行具有重要意义。

* **替换**(Substituting)，指替换现有模块设计。替换通常是因为多种设计路径之间存在竞争关系，于是更多受到经济系统因素的驱动。模块的可替换性通常是由分解所决定的，因此分解在此扮演了非常重要的角色。

* **增强**(Augmenting)和**排除**(Excluding)，即添加或删除模块，与分解与替换不同的是，增强和排除是针对已经模块化的系统来说的。排除体现了模块化设计的可配置性，也就是说用户可以按需选择模块，这与替换的特性是相当的。增强通常是由于系统中需要引入新特性，为了保证可增强性，需要在设计规则阶段就要考虑这种能力。

* **反转**(Inverting)，指创建新的设计规则。我们知道设计规则来自于设计参数，后者广泛存在于隐性模块中。因此有时需要把隐性模块从当前的设计层级中“拉取”上来，使其对更多模块保持可见。

* **移植**(Porting)，即把当前模块移植到新系统。某些隐性模块支持从当前系统移植到新系统，那么该模块至少应满足以下条件之一:

  * 所依赖的设计规则在新系统中存在且不变。

  * 模块本身不受设计规则的影响。

采用上述模块操作符可以模拟任何过去、现在和未来所发生的设计变化。例如可以抽取设计演化历史中的连续片段，然后用模块操作符描述每一步的变化。对于进行中乃至未来的设计来说，模块演化则是非确定的，采用公式(j6 X 2) - 1即可计算模块演化的所有可能路径，例如当系统中包含6个模块时，就有93311种演化可能。

#### 应用DSM分析软件设计

[NEVD05]首次把DSM用于管理复杂软件系统的**依赖模型**(Dependency model)，具体方法是通过静态分析提取代码的依赖关系，然后在DSM中进行层次结构展示，支持人工选取设计规则，并且检测出违反相关规则的依赖关系。

通过静态分析提取到的大多属于语法依赖，即字面引用所体现的依赖关系。不同编程语言的语法依赖类型存在一定区别，并且语言自身的模块化特性也不尽相同，因此存在多种表示软件依赖模型的方式。一种简单的做法是忽略依赖类型间的差异，选择统一的模块化元素作为DSM的设计参数，例如Java中的类，并且按照元素间存在的引用数量定义依赖强度。下例展示了jEdit v4.2的DSM:

![DSM for jEdit v4.2](http://content.hanyi.name/images/design_diagnosis/jedit_dsm.png)

当DSM规模较大时，需要支持进一步显示矩阵中的层次结构。尽管许多现代编程语言都在语法上提供了层次化结构的特性(例如包、类、方法等)，这些信息可被直接用于DSM分层。但是，多数情况下软件的层次结构无法满足[BC00]的模块性标准，这种在实际中十分普遍的情况被称作软件结构的**技术债**。为了方便理解和改进现有系统的模块性，业界开发了许多针对DSM的聚类算法，即从DSM中的元素及其依赖出发，通过重新排列元素顺序实现自动聚类，其中有代表性的方法有:

* [JNW73]采用**矩阵分区算法**把初始矩阵划分成若干子矩阵，使后者满足下三角矩阵的特征，从而消除循环依赖。

  该算法的基本思路是针对每个元素，首先构建可达性(Reachability)集合R(s)与先导(Antecedent)集合A(s)，前者指从该元素出发能到达的所有元素集合，后者指从非当前元素出发能到达或经过该元素的路径的所有元素集合，以及两者交集R(s)A(s)。算法每次迭代选择满足R(s)A(s) = R(s)的元素集合作为当前矩阵的top-levels，然后将其从剩余元素的集合中删除并重复这一过程，直到剩余元素个数为0。矩阵分区算法的优点是实现简单，能够快速筛选出不存在循环依赖的子矩阵，对DSM分层具有一定意义。但是该方法无法满足更多的模块化特性，例如[BC00]中指出的隐性模块间的相互独立性。

* 聚类分析中常用的**启发式算法**同样被用于构建DSM的元素聚类。如果某个系统内存在一系列规模合理且相互独立的子模块，那么这些子模块内的依赖关系一定趋近于DSM对角线，以此推论为基础设计距离惩罚函数作为启发式算法的目标函数[TS94]。与分区算法相比，启发式算法能够实现模块间独立条件下的更优结果，而且实现也比较简单，例如聚类部分采用现有的遗传算法框架[RAC08]。但是，设计软件模块性的目标函数是一项挑战。另外，软件的模块性往往还体现在层次结构方面，这是一般的聚类方法难以同时考虑的。

* 由于DSM本质上是有向图，因此可以采用图算法进行DSM分层。[SYG09]是一种基于图算法的DSM层次聚类方法，首先计算DSM的凝聚图(Condensation graph)，然后找出所有出度为0的结点的所有依赖关系路径，再从拥有最长路径的结点出发构建DSM的层次结构。该方法构建出的层次结构一定满足下三角矩阵，同一层的模块间保持相互独立且允许并行开发。其优点在于使用DSM实际反映出软件的层次结构，从而能够进一步诊断软件的设计问题[RYR15]。

  如果要根据DSM中依赖关系的强弱寻找更优化的层次结构，则可以采用**图聚类**方法[SS07]，特别是针对有向图聚类[FM13]。[SA14]采用**谱聚类**方法对DSM进行重新聚类，该方法建立在DSM中具有较大特征值的特征向量、特征值、模块层次数以及每层模块数等数量之间的相关性基础上，通过对原始DSM进行奇异值分解、分析和降维，计算每个结点在k维空间的线性表示，最后以结点在k维空间中的距离进行聚类。尽管该方法需要指定k值，但是聚类结果依然能正确反映DSM的层次结构。例如:

![Spectral clustering](http://content.hanyi.name/images/design_diagnosis/spectral_clustering.png)

其中(a)是原始DSM，(b)(c)(d)分别表示k=2，k=4，k=8时的谱聚类结果，可以看到随着k值的变化，聚类结果始终能表现出实际DSM的层次结构。 

### 设计度量(Design metrics)

设计度量涉及一系列面向软件设计的度量指标，包括针对整体模块性的度量、接口强度和优先级、扇入/扇出、联通度以及可见度等等。值得注意的是，设计的度量结果并不能直接等价于设计质量，通常可以作为支持设计分析结果的辅助证据，帮助定位具体问题并结合具体上下文制定改进计划。

**模块度**是一种度量整体模块性的指标。[MAC06]认为可以通过计算DSM中元素间的依赖成本，例如依赖的数量和分布模式等，从而实现模块度的间接计算，并且其中存在两种可能的应用场景:

* 比较软件A和软件B的模块性。

* 比较软件A在T时刻和T + N时刻的模块性。

假设DSM的元素数量为n，其中**传播成本**(Propagation cost，Pc)忽略元素所在的位置，假设直接依赖和间接依赖具有同等成本，然后计算所有元素的扇入或扇出数M，则Pc = M / n2。对于整个系统而言，扇入和扇出数是相等的，因此M可以任选其中一种进行计算。**聚集成本**(Clustered cost)把模块内和模块间的依赖进行区别计算，首先指定一个依赖阈值(通常是10%~100%间的数)，并将DSM中被依赖次数超过该阈值的元素计入主控元素，然后根据以下条件计算每项依赖所包含的成本:

* DependencyCost(i -> j | j is a vertical bus) = d
* DependencyCost(i -> j | in same cluster) = d * n^λ
* DependencyCost(i -> j | not in same cluster) = d * N^λ
其中d是表示是否存在i -> j依赖的二进制值，n指模块规模，N指DSM规模。λ是自定义参数。

除了通过依赖成本计算模块度，另一类方法是直接计算模块度。根据模块从内及外且依赖由强变弱的定义，[GG04]提出了一种通用的模块度计算方法，该方法的前提是DSM中已经包含了精确的模块化信息。当DSM中不包含模块化信息，或者需要直接计算系统的实际依赖复杂度时，可采用**奇异值模块度指数**(Singular Value Modularity Index，SMI)[KO11]。该方法通过对DSM进行奇异值分解，然后计算奇异值的下降率从而表示系统模块度。以下面三种典型的结构模式为例:

![Typical structural patterns](http://content.hanyi.name/images/design_diagnosis/different_patterns.png)

从模块性来看，单块(Integral)系统的模块性较差，总线(Bus-modular)系统也比较差，模块化系统则相对较好。对这些模式对应的DSM进行奇异值分解，从而得到上面三种结构的奇异值下降模式:

![Singular value decay pattern](http://content.hanyi.name/images/design_diagnosis/singular_value_decay_pattern.png)

可以看出，单块系统的下降趋势非常陡峭，总线型系统比较陡峭，而模块化系统的下降趋势则相对平滑。基于上述关联关系，可以认为当系统的模块性较差时，奇异值会出现迅速下降的情况(SMI较低)，而模块性较好的系统，奇异值下降则通常比较缓慢(SMI较高)，这种相关性也是上文讨论的谱聚类方法的基本假设。

## 结论

设计诊断包括分析和度量两个方面，其中设计分析主要负责设计的形式化表示，例如本文讨论的DSM工具。在DSM的基础上可以进一步分析和模拟设计演化过程，发现设计缺陷以及优化系统模块性。DSM同样可以用于设计度量，设计度量指标不直接等价于设计质量，但可以指导设计及其改进。除了基本的度量指标外，模块度是度量系统整体模块性的核心，可以用于不同软件之间和相同软件的不同版本之间的模块性评价。

## 引用

BC00, [Design Rules, Vol. 1: The Power of Modularity](https://mitpress.mit.edu/books/design-rules-volume-1)

NEVD05, [Using Dependency Models to Manage Complex Software Architecture](https://lattix.com/dev/files/wp/oopsla05.pdf)

JNW73, [Binary Matrices in System Modeling](https://systemsconcept.org/html/Content/BM_1973%20.pdf)

TS94, [Integration analysis of product decompositions](http://web.mit.edu/eppinger/www/pdf/Pimmler_DTM1994.pdf)

RAC08, [Systematic module and interface definition using component design structure matrix](https://www.tandfonline.com/doi/abs/10.1080/09544820802563226)

SS07, [Graph clustering](http://www.leonidzhukov.net/hse/2018/sna/papers/GraphClustering_Schaeffer07.pdf)

FM13, [Clustering and Community Detection in Directed Networks: A Survey](https://arxiv.org/pdf/1308.0971.pdf)

SYG09, [Design Rule Hierarchies and Parallelism in Software Development Tasks](https://www.cs.drexel.edu/~yfcai/papers/2009/ASE2009_drh.pdf)

RYR15, [Hotspot Patterns: The Formal Definition and Automatic Detection of Architecture Smells](https://apps.dtic.mil/dtic/tr/fulltext/u2/a621415.pdf)

SA14, [A Spectral Analysis Software to Detect Modules in a DSM](https://www.researchgate.net/profile/Andy_Dong/publication/269279676_A_spectral_analysis_software_to_detect_modules_in_a_DSM_Risk_and_change_management_in_complex_systems/links/54dc1a540cf28d3de65ea241.pdf)

MAC06, [Exploring the Structure of Complex Software Designs: An Empirical Study of Open Source and Proprietary Code](https://pdfs.semanticscholar.org/cbfe/a3d346938dba911239502cc97b282668af71.pdf)

GG04, [A Comparison of Modular Product Design Methods on Improvement and Iteration](https://proceedings.asmedigitalcollection.asme.org/proceeding.aspx?articleid=1651497)

KO11, [Degree of Modularity in Engineering Systems and Products with Technical and Business Constraints](https://hal.archives-ouvertes.fr/hal-00571207/document)