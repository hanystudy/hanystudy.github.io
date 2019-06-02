---
layout: post
title: "软件设计与架构笔记(14)"
date: 2019-06-01 23:31:49 -0400
comments: true
categories:
- architecture
tags:
- software design
- software architecture
- agile software design
---

## 敏捷软件设计

早期的软件开发方法源自传统制造业和建筑业，即按照需求、分析、设计、开发、测试、运营等阶段顺序执行。这种线性的软件开发过程被称作**瀑布模型**(Waterfall)。瀑布模型在上世纪70年代逐渐发展成熟，成为软件开发方法的事实标准。随着互联网的出现，软件工业迈入飞速发展，频繁变更的需求和快速更替的技术使瀑布模型遭遇了空前挑战。于是，通过从先进制造业汲取经验，行业一线的职业程序员们开始调整原有方法，90年代先后诞生了**统一过程**(Unified process)、**Scrum**、**极限编程**(Extreme programming)等轻量级软件开发方法。2001年，程序员们从这些方法的核心思想中提炼出了著名的**敏捷宣言**，由此敏捷成为前述一系列软件开发方法的代名词。时至今日，对于需求明确并且依赖成熟技术的软件开发活动来说，严谨且可靠的瀑布模型仍然占有一席之地。敏捷思想则在自互联网时代开启的一系列新兴领域中更受欢迎，也更具备发展空间。

敏捷对软件设计产生了重大影响，正如Martin Fowler所指出的，极限编程不仅宣告了Big Design Up Front的终结，还严重影响了一批热门的技术实践例如UML、框架构建、设计模式[MFL00]。然而软件设计并未因敏捷而消失，敏捷也不意味着无设计或设计灾难，为了与传统瀑布模型的**计划设计**(Planned design)进行区别，敏捷设计被描述为**演进式设计**(Evolutionary design)、**持续设计**(Continuous design)或**浮现式设计**(Emergent design)，或许这些名词有时夹带了浓厚的宣传意味，但是不可否认分析与设计、原则与模式依然是敏捷软件设计的核心，后者的主要特点在于更加强调轻量化的敏捷设计实践。这往往意味着:

* 强调价值交付，交付价值是推动整个软件工业发展的重要经济基础，因此价值应当始终是软件开发的优先选项。

* 强调团队责任，而不是把职责局限于分析师、设计师、XX师等不同工种，从而减少**单点失败**(Single point failure)。

* 强调快速反馈，无论是测试驱动开发还是持续集成，通过尽可能的自动化实现软件设计质量的实时监控，且应保证快速响应。

具备代表性的实践有面向设计一致性的[**代码味道**](http://www.hanyi.name/blog/2019/04/23/software-design-and-architecture-notes-12/)和**重构**、面向功能一致性的**测试驱动开发**(Test driven development)以及面向团队一致性的**结对编程**(Pair programming)、**代码评审**(Code review)和**持续集成**(Continuous integration)等。敏捷正是通过前述一系列实践，从而避免从BDUF走向另一个设计熵增的极端。本文剩余部分将进一步讨论价值交付、团队责任和快速反馈在敏捷软件设计活动中的具体体现。

### 价值交付：扩展—收缩模式(Expand-Contract Pattern)

应对变化是敏捷软件设计的永恒主题。当现有设计发生变化时，这种变化可能通过接口向模块外传递，从而影响更多其它模块。特别对于[公共接口](https://martinfowler.com/bliki/PublishedInterface.html)来说，变更现有设计会产生较高成本，进而影响交付的价值。[MFL14]讨论了一种扩展—收缩模式，其核心思想是在变更设计的同时保持向后兼容，当新设计产生的价值得到验证后再移除旧设计。例如下列代码:

```
class WindowFactory {

    public Window createWindow(String title, int x, int y, int width, int height) {
        ...
    }
}
```

该例中的抽象工厂类WindowFactory能够创建不同类型的Window，参数列表接受窗口名、位置和尺寸等信息，其中位置和尺寸能够使用Rect对象代替，从而有:

```
class WindowFactory {

    public Window createWindow(String title, Rect rect) {
        ...
    }
}
```

如果该接口属于公共接口，那么所有客户端组件都必须被动修改，否则将无法正常工作。更加合理的做法是首先保留原接口(**扩展**):

```
class WindowFactory {

    public Window createWindow(String title, int x, int y, int width, int height) {
        ...
    }

    public Window createWindow(String title, Rect rect) {
        ...
    }
}
```

当客户端组件迁移完成后，再移除失效的接口(**收缩**)。通过采用扩展—收缩模式，能够有效控制设计变更对交付价值的影响，这也是敏捷软件设计的核心目标之一。

同样的模式还被应用于演进式数据库设计[MFL16]，特别是当数据模式发生破坏性修改时(修改表名、列名等操作)，需要保证在迁移阶段同时支持新旧两种数据访问模式。例如当修改表名时，可以通过创建与旧表名相同的视图提供向后支持。当设计需要修改列名时，可以先创建新的列，然后通过触发器实现新旧两列的同步，直至迁移阶段结束再清理旧模式。在真实场景中应根据数据库类型、应用类型等相关上下文决定具体实现，但设计思想仍然遵循扩展—收缩模式。

### 团队责任：模型风暴(Model Storming)

模型风暴是一种即时建模活动，其目的是把设计责任赋予团队而非个人。理论上说模型风暴可以发生在敏捷软件开发过程的任何时间，但通常是由一名用户故事的所有者(Story owner)在进入开发阶段前发起。首先由所有者确保理解所要解决的问题，然后集合若干团队成员(通常是2~3人)进行站立会议(Stand session)。在所有者介绍完背景并确保所有人理解上下文后，团队开始在一个共享建模工具上探索设计方案，直至大家充分理解并达成一致，会议结束(通常是5~10分钟)。

模型风暴有两种应用场景——分析和设计。分析模型风暴主要是帮助团队理解需求，这时应尽可能集合相关干系人(产品负责人、业务分析师、设计师、质量分析师和开发等)，然后通过绘制草图帮助所有人理解原始需求，并澄清相关问题。这一阶段的关键在于鼓励各种干系人参与建模过程，于是应尽量采用包容性建模(Inclusive modeling)及相关工具，避免过度专业和复杂的工具应用，从而促进沟通。常见的包容性工具有白板、索引卡、便利贴、白板纸等。

设计模型风暴是在编写代码前由若干开发人员共同完成的设计活动。根据开发人员的技术背景，建模过程可以采用[UML](http://www.hanyi.name/blog/2019/02/06/software-design-and-architecture-notes-8/)、[类职责协作卡](http://www.hanyi.name/blog/2019/02/11/software-design-and-architecture-notes-9/)(CRC)、[数据流程图](http://www.hanyi.name/blog/2018/10/23/software-design-and-architecture-notes-5/)或一般流程图等包容性工具。具体过程可以参考[前文](http://www.hanyi.name/blog/2019/02/11/software-design-and-architecture-notes-9/)介绍的CRC及其在协作式OOD中的应用。

模型风暴能够促进设计知识在团队中进行传递，从而有效控制软件设计的单点失败风险。

### 快速反馈：设计监测(Design Monitoring)

快速反馈主要是指能够快速验证当前设计的完整性，并在发现设计缺陷时提供警报，这往往需要依赖专业面向软件设计的静态代码分析工具来完成。通常的做法是把相关工具集成进现有的持续集成过程，并作为某种质量检测报告输出，从而实现设计监测。设计监测工具主要通过分析软件结构中的依赖热点(Hotspot)进行，一般有两种途径分析这些热点——**度量**和**模式**。

度量是通过对软件结构中的实体及其依赖关系进行量化分析，从而反映软件模块化的程度。一种可量化的设计原则是[包依赖原则](http://www.hanyi.name/blog/2019/04/23/software-design-and-architecture-notes-12/)，在此基础上的经典Java开源实现即JDepend(现基本停止维护)，该工具以Java语言的包为单位，分别计算每个包的类数量(TC)、具体类数量(CC)、抽象类数量(AC)、传入耦合(Ca)、传出耦合(Ce)、抽象系数(A)、不稳定性(I)、偏离距离(D)，每种度量的具体定义本文不再赘述。

模式主要是指检测依赖中的反模式，后者主要是违反设计原则的实际情况，例如违反包间无环依赖，接口隔离、Liskov替换、依赖倒置等。具体做法是把软件设计中的依赖关系用图表示，然后检测图中存在的违反设计原则的特定模式(Motif)，经典开源实现即Google的[GUERY](https://code.google.com/archive/p/gueryframework/)(停止维护)。该工具能够根据图中的顶点、关系及其路径长度等条件识别特定模式，并且根据Tarjan算法计算强联通子图进而生成凝聚图。

免费工具除前述外，还有针对Java程序的依赖抽取和可视化工具[Class Dependency Analyzer(CDA)](http://www.dependency-analyzer.org/)，CDA能够把相关依赖以UML的形式进行可视化，帮助用户理解并管理复杂软件结构。专业用于依赖分析的商业工具有[Lattix](http://www.lattix.com/)、[SonarGraph](http://www.hello2morrow.com/products/sonargraph)、[Structure 101](http://www.headwaysoftware.com/products/?code=Structure101)、[JArchitect](https://www.jarchitect.com/)/[NDepend](https://www.ndepend.com/)/[CppDepend](https://www.cppdepend.com/)等，本文不再赘述。

值得一提的是，面向软件设计领域无论是免费还是商业工具，尽管其内置的设计规则具有普遍性，同时也支持自定义规则，但通常都存在较高的学习、维护和实施成本，实际上并不能真正达到快速反馈的目的。对处于一线的中小型敏捷团队来说过重，更适用于一些已经具备较高价值的商业软件开发和大型软件组织的架构看护活动。

[ArchUnit](https://www.archunit.org)是一个基于Java语言的开源依赖检查框架，用户通过编写测试断言的形式约束软件结构依赖，并且通过现有单元测试框架如Junit实现自动运行。与前面提到的主流第三方工具相比，该工具也定义了一些具有普遍意义的依赖规则，同时还具有如下优势:

* 直接采用原生语言并作为宿主的测试实现，支持包、类、注解、分层、分片等多种概念实体，使定义复杂的Java代码依赖规则更加容易。

* 采用单元测试的思路，使依赖规则能够更快响应软件结构变化，降低规则维护的成本，真正实现快速反馈。

* 允许开发人员结合价值交付、团队责任等灵活定制依赖规则，特别适用于敏捷软件设计的场景。

该工具的缺点是无法向多数GUI工具那样支持依赖分析，内置规则也不如成熟商业工具丰富，因此要求开发人员深入理解设计原则，并能够结合上下文定制恰当的规则。

## 结论

在一般的敏捷宣传语言中，诸如**大道至简**(You Aren't Gonna Need It，YAGNI)和**恰如其分**(Just enough)等词汇往往被使用且被轻易误解。原因在于脱离了具体的实践，敏捷就只剩下一个以人为本的空壳，并不能反映出源自核心的根本经济动力。因此无论是从软件匠艺(Craftsmanship)还是专业主义(Professionalism)来看，敏捷对开发人员的要求都要更高。反映在软件设计领域，具体就是除了基本的分析和设计方法、原则和模式等知识外，进一步注重软件设计中的价值交付、团队责任以及快速反馈等实践。

## 引用

MFL00, [Is Design Dead?
](https://martinfowler.com/articles/designDead.html)

MFL14, [Parallel Change](https://martinfowler.com/bliki/ParallelChange.html)

MFL16, [Evolutionary Database Design](https://martinfowler.com/articles/evodb.html)