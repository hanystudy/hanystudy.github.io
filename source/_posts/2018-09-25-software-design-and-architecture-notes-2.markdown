---
layout: post
title: "软件设计与架构笔记(2)"
date: 2018-09-25 09:46:21 -0400
comments: true
categories:
- architecture
tags:
- software design
- software architecture
- structured programming
- niklaus wirth
---

## 结构化编程(Structured Programming)：计算语言的突破

上世纪50-60年代，人类的计算能力实现了迅猛发展，各界对计算机的应用也有很高期许，越来越多的领域希望得到强大的计算赋能从而实现飞跃。然而当面临的问题越多、越复杂时，人们在解决问题的道路上发现了一条巨大的鸿沟，即以现有的软件构建理论和方法难以应对这些挑战。机遇与挑战并存，这场**软件危机**(Software Crisis)最终促成了软件工程作为一门独立的学科从计算机科学的襁褓中成长起来。

软件危机这个词最早在1968年的北约组织软件工程会议上被诸多与会者提出[NATO68]，由此引发的技术创新和组织行为思辨至今依然活跃。而更现实的影响是，科学家们首先在编程语言本身找到了突破口——**结构化编程**。

发明于上世纪50年代的ALGOL语言，首次用begin...end语句引入了**代码块**的概念，通过限定其中变量声明的词法作用域，提高程序的**可读性**，从此引起了围绕代码块的研究。1966年，论文[Bohm66]证明使用三种基本的程序结构就能表达任何可计算函数：顺序执行、条件选择和循环迭代，这为随后针对结构化编程的讨论提供了理论依据。1968年，Dijkstra发表了著名的”GOTO语句有害“的观点，并且肯定了如条件选择、循环等语句的应用，同时称GOTO语句应该在所有“高级语言”（这里指除了机器码之外的语言）中被废除[EWD68]。Dijkstra认为应当尽可能减少静态程序和动态运行进程之间的差距，而GOTO语句造成了大量程序难以被理解，即人很难从混乱的静态代码中认识程序的真正意图。这一废除GOTO语句的言论激起旷日持久的争论，反对者认为GOTO所具有的灵活性能满足持续的系统优化工作，但争论两方基本同意应当对GOTO限制使用。于是，结构化编程开始被广泛接受。

伴随着结构化编程的普及，过程式编程(Procedural programming)也在60年代起被许多流行语言采纳，如COBOL和BASIC。这种编程方法以代码块为基础，允许使用子过程（也称子程或函数）编写程序单元，并且可以被程序随时调用。使得来自不同程序员甚至不同组织的代码变得简单可复用，为随后代码库的流行奠定基础。

## 结构化程序设计与分析

结构化编程实现了编程语言的巨大进步，作为首席布道者，Dijkstra发表了很多关于程序的可理解性以及结构化编程实践的原则性观点[EWD70]，但如何设计结构化程序还需要进一步说明。1971年，在计算机教育领域功勋卓著的Niklaus Wirth详细解释了一种自顶而下逐步求精的程序设计方法，并以数学中经典的八皇后问题（把这个著名问题作为编程案例，原因之一是尚无该问题的已知解析解）为例演示了程序设计从问题分析到实现的过程[NW71]。

简单分析可以得到八皇后问题的直观解法：对于全体候选解的集合A，其中每个解元素x满足条件函数p，即(x ∈ A) ∧ p(x)，则：
```
repeat Generate the next element of A and call it x
until p(x) ∨ (no more elements in A);
if p(x) then x = solution
```
由排列组合知识可知，集合A的空间可达2^32，枚举算法效率较低。通过对问题进一步的分析，使用回溯法解决该问题的算法效率较高，即：
```
j := l;
repeat trystep j;
if successful then advance else regress
until (j < 1) ∨ (j > n) 
```
以上述程序分析结果为基础构建程序，按照回溯算法的基本思想，首先依照specification给出初步实现：
```
variable board, pointer, safe;
considerfirstcolumn;
repeat trycolumn;
	if safe then
	begin setqueen; considernextcolumn
	end else regress
until lastcoldone ∨ regressoutoffirstcol
```
根据现有结构化编程语言的表达能力，对如下指令进一步分解：

trycolumn:
```
procedure trycolumn;
repeat advancepointer; testsquare
until safe ∨ lastsquare 
```

regress:
```
procedure regress;
begin reconsiderpriorcolumn
	if ¬ regressoutoffirstcol then
	begin removequeen;
		if lastsquare then
		begin reconsiderpriorcolumn;
			if ¬ regressoutoffirstcol then
				removequeen
		end
	end
end
```
截至目前，如需对上述程序中的指令做进一步分解，就需要设计额外的数据表示了。通过分析待分解语句，可知需要设计一个记录每位皇后位置的数据表示，例如使用二维数组表达棋盘上的每个方块。这里给出一个优化的数据表示方式：
```
integer j (0 ≤ j ≤ 9)
integer array x[1:8] (0 ≤ x[i] ≤ 8) 
```
其中j表示当前被检查的列序号，一维数组x用于存储上一次被检查方块的坐标，程序的部分指令可以被进一步细化为：
```
procedure considerfirstcolumn;
	begin j := 1; x[1] := 0 end
procedure considernextcolumn;
	begin j := j + 1; x[j] := 0 end
procedure reconsidetpriorcolumn; j := j - 1
procedure advancepointer;
	x[j] := x[j] + 1
Boolean procedure lastsquare;
	lastsquare := x[j] = 8
Boolean procedure lastcoldone;
	lastcoldone := j > 8
Boolean procedure regressoutoffirstcol;
	regressoutoffirstcol := j < 1 
```
接下来考虑剩余指令testsquare、setqueen和removequeen。

指令testsqaure需要验证是否满足问题条件，通过已知的x数组应不难通过计算进行判定，问题是可能导致较高的计算量，同时考虑到testsquare的调用频次较高，这里采用额外数据表示进行优化，设计三个Boolean型数组，其意义如下：
```
a[k] = true : no queen is positioned in row k
b[k] = true : no queen is positioned in the /-diagonal k
c[k] = true : no queen is positioned in the \-diagonal k 
```
那么testsquare就可以用简单的布尔运算表示，其余指令也可以通过上述结构完成：
```
procedure testsquare;
	safe := a[x[j]] ∧ b[j+x[j]] ∧ c[j-x[j]]
procedure setqueen;
	a[x[j]] := b[j+x[j]] := x[j-x[j]] := false
procedure removequeen;
	a[x[j]] := b[j+x[j]] := c[j-x[j]] := true 
```
此时发现上述实现的x[j]调用次数过多，为了进一步优化，把x[j]用变量i表示，从而有：
```
procedure testsquare;
	safe := a[i] ∧ b[i+j] ∧ c[i-j]]
procedure setqueen;
	a[i] := b[i+j] := c[i-j] := false
procedure removequeen;
	a[i] := b[i÷j] := c[i-j] := true
procedure considerflrstcolumn ;
	begin j:= 1; i:= 0 end
procedure advancepointer; i := i + l
procedure considernextcolumn
	begin x[j] := i; j:=j+l; i := 0 end
Boolean procedure lastsquare;
	lastsquare := i = 8 
```
通过inline替换程序中的部分指令，其余采用过程调用，从而最终实现如下程序：
```
j := 1; i := 0;
repeat
	repeat i := i + 1 ; testsquare
	until safe ∨ (i = 8);
	if safe then
	begin setqueen; x[j] := i; j := j + 1; i := 0
	end else regress
until (j > 8) ∨ (j < 1);
if i > 8 then PRINT(x) else FAILURE 
```
前述过程清晰解释了逐步求精这种非常经典的结构化程序的分析和设计过程，从早期分析确定适用算法，然后利用基本的结构化编程元素描述初步程序，对复杂过程进一步分解，同时考虑额外必要的数据表示和程序运行效率优化，最终使用目标编程语言实现程序。这是一种具有普遍适用意义的编程方法论，也呼应了Wirth的那句名言：程序=算法+数据结构。

## 结论

50年前的软件危机所揭露的问题成为今天软件工程研究的基石。GOTO语句的争论直至今天，从历史发展看，更多人选择支持Dijkstra的GOTO有害论，许多90年代以后出现的编程语言并没有在应用层面设计GOTO语句。但是，GOTO争论背后有关编程语言灵活和统一的争辩还远未结束。另一方面，结构化编程促成了一套良好的编程方法论，迄今Wirth的逐步求精方法还被采用于程序设计课程，为计算机教育的普及和广泛应用打下了坚实基础。同时，软件设计所要解决的问题也得以提升到更高的复杂度水平。

## 引用

NATO68, [NATO Software Engineering Conference](http://homepages.cs.ncl.ac.uk/brian.randell/NATO/nato1968.PDF)

Bohm66, [Flow Diagrams, Turing Machines and Languages with Only Two Formation Rules](http://www.cs.unibo.it/~martini/PP/bohm-jac.pdf)

EWD68, [Go-to statement considered harmful](https://www.cs.utexas.edu/users/EWD/ewd02xx/EWD215.PDF)

EWD70, [Notes on structured programming](https://pure.tue.nl/ws/files/2408738/252825.pdf)

NW71, [Program Development by Stepwise Refinement](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.119.8111&rep=rep1&type=pdf)