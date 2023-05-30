---
title: 从大学数学到小学数学：从集合论证明算术基本定理
date: 2022-10-17 14:35:10
category: Mathematics
tags:
 - Elementary Math
 - Set Theory
 - Elementary Number Theory
mathjax: true
---

2022年，通读了《陶哲轩实分析》一书，其从Peano公理出发依次定义自然数、整数、有理数、实数和加减乘除、极限等运算，一气呵成、让人膜拜。

因为是关于数学分析的书，Tao只在第三章讲了集合论，而数理逻辑还在附录里。

但是我们常常听说“集合论是现代数学的基石”，那么理所当然是可以在定义数系之前，先做集合论的公理化。

因此我也想做一次尝试，在不做其他任何预设的情况下，完全从数学公理出发，进行一次“严谨的”推导。

当然有很多离散数学、集合论的课程就是这样做的。

不过个人能力有限，打算只证明一个小学生都知道的简单定理——算术基本定理，就收工。

<!--more-->

内容主要涉及到4个部分：
- 数理逻辑，可参照Kenneth H. Rosen的《离散数学及其应用》
- 集合论，可参照Herbert B. Enderton的《Elements of Set Theory》和维基百科关于集合论公理化的词条
- 数系（只需要自然数和整数），可参照陶哲轩的《Analysis I》即《陶哲轩实分析》的前4章、Herbert B. Enderton的《Elements of Set Theory》
- 数论，可参照陈景润的《初等数论》（*这里要吐槽一下哈尔滨工业大学出版社，这本书出版得很“粗糙”，分三册然后几十页的后记重复三次先不说，还有我等凡夫俗子肉眼可见的错误、很多还是明显的排版错误就有一大堆，所以这也是“逼迫”我自己推导一次的动力吧*）

## 一、数理逻辑

### 1）命题逻辑

#### 定义：命题是一个陈述句所表达的判断，具有真值，即这句话不是真的（True）就是假的（False）。

例如：“雪是白色的”。

**命题不等同于句子**：例如，“雪是白色的”和“白色是雪的颜色”是不同的句子，但它们判断相同的事，所以是相同的命题。
**命题不依赖于语言**：例如，“雪是白的”和“Snow is white”是相同的判断。

疑问句、祈使句、感叹句都不能表达命题。

真值为“真”时，记作 T，为“假”时，记作 F。

#### 逻辑运算符

1. （否定）逻辑“非”：如果命题$p$的真值为T，则$\neg p$为F，如果命题$p$的真值为F，则$\neg p$为T。

2. （合取）逻辑“与”：如果命题$p$和$q$的真值都为T，则$p\wedge q$为T。反之，如果命题$p$和$q$中有一个真值为F，则$p\wedge q$为F。

3. （析取）逻辑“或”：如果命题$p$和$q$中有一个真值为T，则$p\vee q$为T。反之，如果命题$p$和$q$的真值都为F，则为$p\vee q$为F。

4. （蕴涵）如果假定命题$p$的真值为T，可以推导出命题$q$也为T，则称命题$p$蕴涵$q$，记作$p\implies q$。

利用命题和否定的定义，就可以得到数学证明中常用的反证法。

**反证法**：给定一个命题$p$和它的否定命题$\neg p$，则两者中如果一个是假的，另一个就必然是真的。所以在证明某一个命题$p$时，可以通过证明$\neg p$为假，从而“反过来”证明$p$为真。

关于$p\implies q$，可以定义：
- $p$为$q$的充分条件
- $q$为$p$的必要条件
- $p\implies q$称为原命题
- $q\implies p$称为逆命题
- $\neg p\implies \neg q$称为否命题
- $\neg q\implies \neg p$称为逆否命题

对于一个假命题$p$，即可推导出真命题，也可推导出假命题，所以$p$为假时，$p\implies q$是恒为真的。

也就是说，如果以假命题为条件出发，推导得出的数字结论没有意义。

这也是为什么，现代数学这么强调公理化，并且对公理之间的兼容性吹毛求疵的原因。

**等价关系**：如果$p\implies q$且$q\implies p$为真，则称命题$p$与$q$等价，记作$p\iff q$，文字上常常写作“$p$当且仅当$q$”。

命题逻辑里最“简单”的证明方法就是使用真值表。

下面证明，如果$p$与$q$等价，那么$p$为真且$q$为真，或者$p$为假且$q$为假，即$p$和$q$一定同真或同假。

`证明`：根据定义，有$p\iff q$即$(p\implies q) \wedge (q\implies p)$真值表：
$$\begin{array}{c|c|c|c|c}
p&q&p\implies q& q\implies p & p\iff q \\\
T&T&T&T&T \\\
T&F&F&T&F \\\
F&T&T&F&F \\\
F&F&T&T&T
\end{array}$$

通过真值表可以看到，当$p\iff q$为T时，必须$p\implies q$和$q\implies p$同时为真，这时$p$和$q$只能是同真或同假。反之，当$p$和$q$同真或同假时，$p\iff q$也一定为真。$\square$

下面证明，原命题与逆否命题等价。

`证明`：根据蕴涵的定义，原命题的真值表为：
$$\begin{array}{c|c|c}
q& p & q\implies p \\\
T&T&T \\\
T&F&F \\\
F&T&T \\\
F&F&T
\end{array}$$
逆否命题的真值表为：
$$\begin{array}{c|c|c|c|c}
p&q& \neg p & \neg q & \neg q\implies \neg p \\\
T&T&F&F&T \\\
T&F&F&T&F \\\
F&T&T&F&T\\\
F&F&T&T&T
\end{array}$$
可以看到不论$p$和$q$的真假，原命题与逆否命题都同真或同假，所以原命题与逆否命题等价。$\square$

### 2）一阶逻辑

一阶逻辑和命题逻辑的不同之处在于，一阶逻辑包含**量词**。

1. 存在：$\exists$
2. 任意：$\forall$

所以在一阶逻辑中，可以使用逻辑“变量”$x$。例如，命题$x+1=2$，只有在$x=1$时为真。

通过一阶逻辑，我们的数学表达能力大大提升了。

后续的公理，除了文字表述，我也尽量给出逻辑符号的形式化表述。

### 3）相等公理

等价只是描述了两个命题的关系，而对于一般的数学对象，需要引入相等关系。

#### 1. <font color=red>（自反公理）对任意的对象$x$，有$x=x$。</font>

$\forall x : x=x$

#### 2. <font color=red>（对称公理）对任意的对象$x$和$y$，如果$x=y$，那么$y=x$。</font>

$\forall x, \forall y : x=y \implies y=x $

#### 3. <font color=red>（传递公理）对任意的对象$x$、$y$和$z$，如果$x=y$且$y=z$，那么$x=z$。</font>

$\forall x, \forall y, \forall z : x=y \wedge y=z \implies x=z $

#### 4. <font color=red>（替换公理）对任意的对象$x$和$y$，如果$x=y$，那么对任意一个关于$x$的命题$P(x)$，有$P(x)$和$P(y)$等价。</font>

$\forall x, \forall y : x=y \implies (P(x) \iff P(y)) $

## 二、集合论

通常理解，集合是由一些数学对象组成的整体。

构成集合$A$的对象$a$称作“元素”或“成员”，记作$a \in A$。当元素$a$不属于集合$A$时，记作$a \notin A$。

### 1）朴素集合论

在朴素集合论中，可以使用一个非常简练的概括公理完成集合论的定义，但是其中却存在着悖论。

**伪！（概括公理）**：对任意一个关于$x$的命题$P(x)$，满足$P(x)$为真的对象$x$构成一个集合。

$\exists A, \forall x: x \in A \iff P(x)$

这个集合可以记作 $A = \lbrace x | P(x) \rbrace$。

<font color=blue>**罗素悖论**：不存在一个集合是由“所有不包括自身的集合”所组成的。</font>

`证明`：设命题$P(x)$为“所有不包括自身的集合$x$”，即$x \notin x$，根据概括公理，满足这个命题的对象$x$构成一个集合，设为集合$A = \lbrace x | x \notin x \rbrace$。
然后讨论命题$A \in A$的真伪：
如果$A \in A$为真，那么$A$是自身的元素，根据集合$A$的定义，则满足命题$P(x)$，即$A \notin A$，矛盾；
如果$A \notin A$为真，那么满足命题$P(x)$，则根据集合$A$的定义，$A$是自身的元素，即$A \in A$，矛盾。$\square$
所以数学需要更严格的公理化集合论，此处使用常见的ZFC集合论。

### 2）公理化集合论

#### 1. <font color=red>（外延公理）如果两个集合有相同的元素，那么它们相等。</font>

$\forall A, \forall B : (\forall x : x \in A \iff x \in B) \implies A=B$

#### 2. <font color=red>（空集公理）存在一个没有元素的集合。</font>

$\exists A, \forall x : x \notin A$

通过外延公理可以证明这个集合是唯一的，称这个集合为**空集**，记作 $\varnothing$。

#### 3. <font color=red>（配对公理）对任意两个集合$x$和$y$，存在一个集合有且只有$x$和$y$作为元素。</font>

$\forall x, \forall y, \exists A, \forall z : z \in A \iff z=x \vee z=y$

配对公理可以构造双元素集合，记作$\lbrace x,y\rbrace$。

当$x=y$时，也可以构造单元素集合：即对任意集合$x$，存在集合$\lbrace x,x\rbrace$，记作$\lbrace x\rbrace$。

#### 4. <font color=red>（并集公理）对任意集合$A$，存在一个集合$B$，$x$是$B$的元素当且仅当$x$是$A$的某个元素的元素。</font>

$\forall A, \exists B, \forall x : x \in B \iff (\exists y : x \in y \wedge y \in A)$

通过外延公理可以证明这个集合是唯一的，称这个集合为$A$的**并集**，记作 $\bigcup A$。

通过配对公理可以得到一个更容易理解的形式：对任意两个集合$A$和$B$，存在一个集合，其元素属于$A$或者属于$B$。

$\forall A, \forall B, \exists C, \forall x : x \in C \iff x \in A \vee y \in B$

`证明`：根据配对公理，对任意两个集合$A$和$B$，存在一个集合$\lbrace A,B\rbrace$。对集合$\lbrace A,B\rbrace$，根据并集公理，得到存在一个集合$C$，满足$x$是$C$的元素当且仅当$x$是$\lbrace A,B\rbrace$的元素的元素，即$x$是$A$的元素或$x$是$B$的元素。$\square$

通过外延公理可以证明这个集合是唯一的，称这个集合为$A$和$B$的**并集**，记作 $A \cup B$。

#### 5. <font color=red>（分类公理、子集公理）对任意集合$A$，存在一个集合$B$，$x$是$B$的元素当且仅当$x$是$A$的元素且命题$P(x)$成立。</font>

$\forall A, \exists B, \forall x: x \in B \iff x \in A \land P(x)$

这个集合$B$称作$A$的一个**子集**，记作 $B \subseteq A$，即 $B \subseteq A \iff (\forall x : x \in B \implies x \in A)$。

当$B \subseteq A$且$A \subseteq B$时，即$(\forall x : x \in B \implies x \in A) \wedge (\forall x : x \in A \implies x \in B)$，所以$\forall x : x \in B \iff x \in A$，根据外延公理，有$B=A$。

当$B \subseteq A$且$B \neq A$时，集合$B$称作$A$的一个**真子集**，记作 $B \subset A$。

通常定义子集的方式为$B=\lbrace x \in A | P(x)\rbrace$。

使用分类公理，对任意非空集合$A$，可以定义集合$A$的**交集**$\bigcap A$，即$\bigcap A$的元素都是$A$的任意元素的元素。

<font color=green>**推论**：对任意非空集合$A$，它的交集存在。</font>

`证明`：因为$A$不是空集，所以存在一个元素$c \in A$，设命题$P(x)$为$\forall y \neq c (y\in A \implies x \in y)$，根据分类公理，存在一个集合$B$，满足$x \in B \iff x \in c \wedge \forall y \neq c(y \in A \implies x \in y) \iff \forall y (y\in A \implies  x \in y)$。$\square$

对任意两个集合$A$和$B$，存在一个集合，其元素属于$A$并且属于$B$，称这个集合为$A$和$B$的**交集**，记作 $A \cap B$。

#### 6. <font color=red>（幂集公理）对任意集合$A$，存在一个以$A$的全部子集为元素的集合。</font>

$\forall A, \exists B, \forall x: x \in B \iff x \subseteq A$

通过外延公理可以证明这个集合是唯一的，称这个集合是$A$的**幂集**，记作 $\mathcal{P}(A)$。

#### 7. <font color=red>（无穷公理）存在一个集合$\mathbf{N}$，使得空集是$\mathbf{N}$的元素，并且如果$x$是$\mathbf{N}$的元素，那么$x$与它的单元素集合$\lbrace x\rbrace$的并集也是$\mathbf{N}$的元素。</font>

$\exists \mathbf{N}: \varnothing \in \mathbf{N} \land (\forall x: x \in \mathbf{N} \implies x \cup \lbrace x\rbrace \in \mathbf{N})$

这种集合也称作**归纳集合**，其中可以定义集合$x$的**后继**为$x \cup \lbrace x\rbrace$。

这个公理的本质是：存在一个集合包含所有的自然数。

#### 8.  <font color=red>（替代公理）对任意集合$A$，存在一个集合$B$，$y$是$B$的元素当且仅当存在$x$是$A$的元素且命题$P(x,y)$为真。</font>

其中$P$是一个双变量谓词，对任何集合$x$存在唯一的集合$y$使$P(x,y)$成立，定义$F(x)=y$当且仅当$P(x,y)$。

$\forall A, \exists B, \forall y: y \in B \iff \exists x \in A : y = F(x)$

分类公理可以从替代公理和空集公理共同得出。

#### 9. <font color=red>（正则公理）对任意非空集合$A$，至少存在一个元素$x$，$x$与$A$的交集为空集</font>

$\forall A: A \neq \varnothing \implies \exists x \in A : x \cap A = \varnothing$

<font color=green>推论：不存在以自身为元素的集合。</font>

`证明`：反证法，假设存在一个集合$A$，使得$A$是自身的一个元素，即$A \in A$。根据配对公理，存在集合$B = \lbrace A\rbrace$。由于$B$中只有一个元素$A$，根据正则公理，有$A \cap \lbrace A\rbrace= \varnothing$。但根据假设有$A \in A$且$A \in \lbrace A\rbrace$，所以$A \cap \lbrace A\rbrace= \lbrace A\rbrace \neq \varnothing$，矛盾。$\square$

#### 10. <font color=red>（选择公理）给定一个集合$x$，其元素皆为互不相交的非空集合，那么存在一个集合$y$，包含$x$每一个元素的仅仅一个元素。</font>

$\forall X \left[ \varnothing \notin X \implies \exists f : X \rightarrow \bigcup X \quad \forall A \in X \, ( f(A) \in A ) \right]$

## 三、自然数

### 1）Peano公理

对自然数最“自然”的定义，就是使用**Peano公理**：

#### 1. <font color=red>$0$是一个自然数。</font>

#### 2. <font color=red>如果$n$是一个自然数，那么$n$的后继也是一个自然数。</font>将$n$的后继记作$n++$。

#### 3. <font color=red>$0$不是任何自然数的后继。</font>等价于，对任意一个自然数$n$，有$n++$不等于$0$。

#### 4. <font color=red>对于不同的自然数，它们的后继也不同。</font>等价于，如果$n++=m++$，那么$n=m$。

#### 5. <font color=red>（数学归纳法）令$P(n)$表示对自然数的任意一个命题，如果$P(0)$为真且当$P(n)$为真时有$P(n++)$也为真，那么对任意自然数$n$，都有$P(n)$为真。</font>

一般把全体自然数的集合记作$\mathbf{N}$。

通过集合论中的无穷公理和分类公理可以得到$\mathbf{N}$的存在性，通过外延公理可以证明$\mathbf{N}$的唯一性。

### 2）构造自然数

当然，更“纯粹”地，我们还可以直接从集合论公理中构造出自然数，试一试：

1. 根据空集公理，存在空集$\varnothing$，定义$0=\varnothing$；（对应Peano公理的第一条）
2. 定义集合$n$的后继为$n \cup \lbrace n\rbrace$，即定义$1=0++=0 \cup \lbrace 0\rbrace= \varnothing \cup \lbrace \varnothing\rbrace= \lbrace \varnothing\rbrace$，依次类推可以定义$2=\lbrace \varnothing, \lbrace \varnothing\rbrace \rbrace$、$3=\lbrace \varnothing, \lbrace \varnothing\rbrace, \lbrace \varnothing, \lbrace \varnothing\rbrace \rbrace \rbrace$等等；（根据自然数的定义，对应可以证明Peano公理的第二、三、四、五条）

根据无穷公理，包括$\varnothing$及其全部后继的集合是存在的，可以把这一类集合称为归纳集合（inductive set）。

根据后继的定义，由$n \in n \cup \lbrace n\rbrace$且$n \subseteq n \cup \lbrace n\rbrace$，可以得到对任意自然数$n \in n++$且$n \subseteq n++$。

<font color=green>**推论**：$0$不是任何自然数的后继。</font>

`证明`:反证法，假设$0=n++$，即$\varnothing = n \cup \lbrace n\rbrace$，因为存在元素$n \in \lbrace n\rbrace$，根据并集公理$n \in n \cup \lbrace n\rbrace$，即$n \in \varnothing$，但是这与空集公理矛盾。$\square$

**自然数定义**：把包括$\varnothing$及其全部后继的“最小”集合称为自然数集。即对任意的归纳集合，自然数都是它们的元素，而自然数集就是全体自然数的集合。

<font color=blue>**定理1**：自然数集的存在性。即存在一个集合，它的元素是任意归纳集合的元素。</font>

`证明`：设$A$是一个归纳集合，根据无穷公理，这个集合是存在的。根据分类公理，设命题$P(x)$为“$x$是除$A$以外任意归纳集合的元素”，则存在一个集合$B$，满足
$x \in B \iff x \in A \wedge P(x) \iff x \text{是任意归纳集合的元素}$。$\square$

注意，这里不能直接从全体归纳集合的交集得到自然数集，因为“全体归纳集合”不是一个明确定义的集合。

<font color=blue>**定理2**：自然数集$\mathbf{N}$是归纳集合，并且$\mathbf{N}$是任意归纳集合的子集。</font>

`证明`：首先，根据归纳集合的定义，空集$\varnothing$是任意归纳集合的元素，所以$\varnothing \in \mathbf{N}$。然后，对于某个自然数$n \in \mathbf{N}$，自然数$n$是任意归纳集合的元素，则由归纳集合的定义，$n++$也是任意归纳集合的元素，则$n++ \in \mathbf{N}$，所以自然数集$\mathbf{N}$也是归纳集合。根据子集的定义，自然数集$\mathbf{N}$是任意归纳集合的子集。$\square$

<font color=blue>**定理3（数学归纳法）**：自然数集$\mathbf{N}$的任意子集，如果它是归纳集合，那么它等于自然数集。</font>

`证明`：设自然数集的子集$T=\lbrace n \in \mathbf{N} | P(x) \rbrace$，即$T \subseteq \mathbf{N}$为归纳集合；根据定理2，自然数集是归纳集合的子集，即$\mathbf{N} \subseteq T$，所以$T = \mathbf{N}$。$\square$

<font color=blue>**定理4**：任意一个非$0$的自然数都是某个自然数的后继。</font>

`证明`：设自然数集的子集$T=\lbrace n \in \mathbf{N} | n = 0 \vee (\exists p \in \mathbf{N}: n = p++) \rbrace$。因此$0 \in T$，并且如果$m \in T$，根据子集的定义，则$m \in \mathbf{N}$及$m++ \in \mathbf{N}$，即对于$m++$来说存在$p = m \in \mathbf{N}$满足$m++ = p++$，那么$m++ \in T$。因此$T$是归纳集合，根据定理3（数学归纳法），可得$T = \mathbf{N}$。$\square$

为了证明Peano假设的第四条，需要先引入一个“传递集”（transitive set）的概念。

**传递集**：如果集合$A$的元素的任意元素也是$A$的一个元素，那么集合$A$就被称为传递集，即$\forall x, \forall a : x \in a \wedge a \in A \implies x \in A$。

等价表述有：集合$A$的并集是自身的子集$\bigcup A \subseteq A$，集合$A$的元素也是自身的子集$a \in A \implies a \subseteq  A$。

<font color=blue>**定理5**：对于传递集$a$，有$\bigcup (a++) = a$。</font>

`证明`：根据后继的定义、集合并集的运算规则以及传递集的性质$\bigcup A \subseteq A$，有$\bigcup (a++) = \bigcup (a\cup \lbrace a\rbrace) = \bigcup a \cup \bigcup\lbrace a\rbrace= \bigcup a \cup a = a$。$\square$

<font color=blue>**定理6**：任意自然数是一个传递集。</font>

`证明`：设命题$P(n)$为“自然数$n$是一个传递集”，根据分类公理，存在一个集合$T=\lbrace n \in \mathbf{N} | P(n)\rbrace$。下面，通过定理3（数学归纳法）来证明$T=\mathbf{N}$。首先显然有$\varnothing \in T$，然后假设$k \in T$，根据定理5，有$\bigcup (k++) = k \subseteq k++$，所以$k++$是一个传递集，即$k++ \in T$，因此集合$T$是一个归纳集合，满足定理3。$\square$

<font color=green>**推论**：如果自然数$n++=m++$，那么$n=m$。</font>

`证明`：如果$n++=m++ \in \mathbf{N}$，则$\bigcup(n++)=\bigcup(m++)$。根据定理6，可知$n++$和$m++$都是传递集，根据定理5，则有$\bigcup(n++)=n$和$\bigcup(m++)=m$，所以$n=m$。

<font color=blue>**定理7**：自然数集$\mathbf{N}$是一个传递集。</font>

`证明`：即需要证明$\forall n \in \mathbf{N} : n \subseteq  \mathbf{N}$。根据分类公理，存在一个集合$T=\lbrace n \in \mathbf{N} | n \subseteq  \mathbf{N}\rbrace$。显然$\varnothing \in T$，然后假设$k \in T$，则有$k \subseteq \mathbf{N}$，同时$\lbrace k\rbrace \subseteq \mathbf{N}$，因此$k \cup \lbrace k\rbrace \subseteq \mathbf{N}$，即$k++ \in T$。所以集合$T$是一个归纳集合，根据定理3（数学归纳法）可知$T=\mathbf{N}$。$\square$

## 四、运算

上面通过纯粹集合论的构造，已经证明了自然数集的存在以及唯一性了，同时一般的Peano公理五条都成立。为了将自然数扩充到整数，需要引入加法、乘法、减法等运算，而严格的定义“运算”需要使用集合论的笛卡尔积，所以这里先回到集合论。

### 1）关系（有序对、笛卡尔积、函数）

**定义（有序对）**：$<x,y>$定义为$\lbrace \lbrace x \rbrace,\lbrace x,y\rbrace \rbrace$。

<font color=green>**推论**：$<u,v>=<x,y>$当且仅当$u=x$且$v=y$。</font>

`证明`：首先，当$u=x$且$v=y$，根据有序对的定义和相等公理，显然有$<u,v>=<x,y>$。

然后证明另一个方向，假设$<u,v>=<x,y>$，即$\lbrace \lbrace u \rbrace,\lbrace u,v\rbrace \rbrace = \lbrace \lbrace x \rbrace,\lbrace x,y\rbrace \rbrace$。

根据外延公理，有$\lbrace u \rbrace \in \lbrace \lbrace x \rbrace,\lbrace x,y\rbrace \rbrace$且$\lbrace u,v \rbrace \in \lbrace \lbrace x \rbrace,\lbrace x,y\rbrace \rbrace$。

对$\lbrace u \rbrace \in \lbrace \lbrace x \rbrace,\lbrace x,y\rbrace \rbrace$，有（a）$\lbrace u \rbrace = \lbrace x \rbrace$或者（b）$\lbrace u \rbrace = \lbrace x,y \rbrace$；

对$\lbrace u,v \rbrace \in \lbrace \lbrace x \rbrace,\lbrace x,y\rbrace \rbrace$，有（c）$\lbrace u,v \rbrace = \lbrace x \rbrace$或者（d）$\lbrace u,v \rbrace = \lbrace x,y \rbrace$。

对（a）（b）（c）（d）分情况讨论:

首先，假设（b）成立，则$u=x=y$，所以（c）和（d）是等价的，并且有$u=v=x=y$。这种情况下，推论成立。

同理，假设（c）成立，这种情况跟（b）是对称的，推论也成立。

然后，假设（b）和（c）都不成立，即（a）和（d）同时成立时，根据（a）有$u=x$，根据（d）有$u=y$或者$v=y$，当$u=y$时（b）就成立，与假设矛盾，所以只能$v=y$，即推论成立。$\square$

**定义（笛卡尔积）**：对两个集合$A$和$B$，将它们的元素$x \in A$和$y \in B$所组成的全体有序对$<x,y>$称作集合$A$和$B$的笛卡尔积（Cartesian product），记作$A \times B$：$$A \times B = \lbrace <x,y>|x \in A \wedge y \in B \rbrace$$

注意，这里有一个问题，我们还没有证明笛卡尔积的存在性，即没有从集合论公理出发，证明存在一个更大的集合包含了集合$A$和$B$的笛卡尔积。

<font color=blue>**引理**：如果$x$和$y$都是集合$C$的元素，那么有序对$<x,y>$属于$C$的幂集的幂集，即$\forall x \in C, y \in C: <x,y> \in \mathcal{P}(\mathcal{P}(C))$。</font>

`证明`：根据子集的定义，$x \in C$和$y \in C$，有子集$\lbrace x \rbrace \subseteq C$和$\lbrace x,y \rbrace \subseteq C$。根据幂集公理，则有$\lbrace x \rbrace \in \mathcal{P}(C)$和$\lbrace x,y \rbrace \in \mathcal{P}(C)$，所以$\lbrace \lbrace x \rbrace, \lbrace x,y \rbrace \rbrace \subseteq \mathcal{P}(C)$。再由幂集公理，有$\lbrace \lbrace x \rbrace, \lbrace x,y \rbrace \rbrace \in \mathcal{P}(\mathcal{P}(C))$，根据有序对的定义，即$<x,y> \in \mathcal{P}(\mathcal{P}(C))$。$\square$

<font color=green>**推论**：对任意的集合$A$和$B$，存在一个集合，它的元素恰是任意$x \in A$和$y \in B$的有序对$<x,y>$。</font>

`证明`：根据子集公理，构造一个集合$\lbrace w \in \mathcal{P}(\mathcal{P}(A \cup B))|w = <x,y> \iff x \in A \wedge y \in B \rbrace$。首先，显然这个集合只包含有序对。同时，根据引理，有序对$<x,y>$都是这个集合的元素。通过这个推论，即完成了笛卡尔积的定义。$\square$

**定义（关系）**：关系是有序对组成的集合，即集合$A$和$B$的关系是笛卡尔积$A \times B$的子集，记作$R$。有时，也把$<x,y> \in R$，记作$xRy$。

**定义（定义域和值域）**：关系$R$的定义域，记作$dom(R)$，为$x \in dom(R) \iff \exists y:<x,y> \in R$；关系$R$的值域，记作$ran(R)$，为$y \in ran(R) \iff \exists x:<x,y> \in R$；关系$R$的域，记作$fld(R)$，为$dom(R) \cup ran(R)$。

**定义（等价关系）**：如果集合$A$上的二元关系$R$满足以下条件：

1. （自反性）$\forall x \in A: xRx$
2. （对称性）$\forall x,y \in A: xRy \implies yRx$
3. （传递性）$\forall x,y,z \in A: (xRy \wedge yRz) \implies xRz$

则称$R$是一个定义在$A$上的等价关系。习惯上把等价关系记作符号$\sim$。

<font color=blue>**引理**：如果$R$是满足对称性和传递性的关系，那么$R$是域$fld(R)$上的等价关系。</font>

`证明`：首先，任意关系$R$都是$fld(R)$上的二元关系，因为$R \subseteq dom(R)\times ran(R) \subseteq fld(R)\times fld(R)$。只需要证明$R$在$fld(R)$上满足自反性，根据对称性和传递性，对定义域上的某个元素有
$$x \in dom(R) \implies xRy \implies xRy \wedge yRx \implies xRx $$
对值域同理。$\square$

**定义（等价类）**：如果$R$是一个等价关系且$x\in fld(R)$，那么称集合$[x]_R = \lbrace t \in fld(R) | xRt \rbrace$为$x$的等价类。如果关系$R$的涵义能够从上下文得到，可以简记为$[x]$。

<font color=blue>**引理**：如果$R$是集合$A$上的等价关系，那么$[x]_R=[y]_R$当且仅当$xRy$。</font>

`证明`：首先假设$[x]_R=[y]_R$，来证明$xRy$。根据自反性，$y \in [y]_R$，因为$[x]_R=[y]_R$，所以$y \in [x]_R$，根据等价类的定义，则有$xRy$。然后假设$xRy$，来证明$[x]_R=[y]_R$。根据传递性，对任意的$t \in [y]_R \implies yRt \implies xRt \implies t \in [x]_R$，所以$[y]_R \subseteq [x]_R$；对称地，同理可得$[x]_R \subseteq [y]_R$，所以$[x]_R=[y]_R$。$\square$

**定义（划分）**：定义集合$A$的划分$\Pi$是一个$A$的非空子集的集合，使得这些子集是互不相交的且无遗漏的，即：

1. $\Pi$中的任意两个不同元素（$A$的非空子集）没有共同元素（交集为空）；
2. 集合$A$的任意元素都是$\Pi$中某个元素的元素。

<font color=blue>**引理**：如果$R$是集合$A$上的等价关系，那么所有等价类组成的集合$\lbrace [x]_R | x \in A \rbrace$是集合$A$的一个划分。</font>

`证明`：因为存在$x \in [x]_R$，所以任意等价类$[x]_R$是非空集合。因为$R$是集合$A$上的等价关系，根据等价类的定义，所以任意$[x]_R$是集合$A$的子集。只需要证明不同的等价类没有共同元素：假设$[x]_R$和$[y]_R$有一个共同元素$t$，那么有$xRt$且$yRt$，因此$xRy$，根据上面的引理有$[x]_R=[y]_R$。$\square$

**定义（函数）**：函数是一种关系$F$，满足对定义域$dom(R)$中的任意元素$x$，有且仅有一个$y$，使得$xFy$。

对于一个函数$F$和一个元素$x$（从几何的角度，即“点”），这个唯一满足$xFy$的$y$称为$F$在$x$处的值，记作$F(x)$。

数学中有很多表述函数的方式，例如说$F$是从$A$到$B$的函数，或者说$F$把$A$映射到$B$（记作$F:A \rightarrow B$），都等价于说$F$是一个函数，$dom(F) = A$并且$ran(F) \subseteq B$。

特别地，当$ran(F) = B$时，称$F$为满射。

当对任意的$y \in ran(F)$，有且仅有一个$x$满足$xFy$时，称$F$为单射。

当$F$即是单射也是满射时，称为双射或一一映射。

对于一个集合$S$，例如自然数集$\mathbf{N}$，一般把函数$F:S \rightarrow S$称为一元运算，例如绝对值、三角函数等；把函数$F:S \times S \rightarrow S$称为二元运算，例如算术中的加减乘除。

对于二元运算，例如加法$+$，一般把函数$+(<x,y>)$写作$+(x,y)$或者$x+y$。

### 2）加法

为了定义自然数集上的运算（加法和乘法），需要引入序列和递归定义的概念：

**定义（序列）**：对集合$A$，如果存在一个从$\lbrace 1,2,\dots,n \rbrace$到$A$的函数，其中$\forall i \in \lbrace 1,2,\dots,n \rbrace: f(i)=a_i \in A$，则把$n$元有序对（或称$n$元组）$(a_1,a_2,\dots,a_n)$称为有限序列，简记为$(a_n)$。把从自然数集$\mathbf{N}$到$A$的函数所形成的序列$(a_0,a_1,a_2,...,a_n,...)$，称为无限序列。

<font color=blue>**引理（递归定义）**：如果对集合$A$，$a \in A$且存在函数$F: A \rightarrow A$，那么存在唯一的函数$h: \mathbf{N} \rightarrow A$使得$h(0)=a$且对任意的自然数$n$有$h(n++)=F(h(n))$。即如果使用记号$a_n=h(n)$，则（无限）序列$(a_0,a_1,a_2,...,a_n,...)$存在且唯一。</font>

`证明`：把一个函数$v$称为“可接受的”，当且仅当$dom(v) \subseteq \mathbf{N} \wedge ran(v) \subseteq A$，并且满足以下条件：

1. 如果$0 \in dom(v)$，那么$v(0)=a$；
2. 如果$\forall n \in \mathbf{N}:n++ \in dom(v)$，那么$n \in dom(v)$且$v(n++)=F(v(n))$。

设$B$是所有可接受的函数组成的集合，并且设$h=\bigcup B$，那么可以证明$h$满足引理的要求。即（1）$h$是一个函数；（2）$h$是可接受的；（3）$dom(h) = \mathbf{N}$；（4）$h$是唯一的。

这个证明比较繁琐，参见Herbert B. Enderton的《Elements of Set Theory》第74-75页，就不推了（实在推不动了）。$\square$

**定义（加法）**：定义二元运算“$+$”，满足对任意自然数$m$，有$0+m=m$；并且对任意自然数$n$和$m$，有$(n++)+m=(n+m)++$。

固定$m$的值，那么运算$+$其实是对$n \in \mathbf{N}$定义的函数$h$，满足$h(0)=m$且$h(n++)=h(n)++$。通过递归定义的引理，可以看到加法是良定义的。

<font color=green>**推论**：加法运算是封闭的，即$+: \mathbf{N} \times \mathbf{N} \rightarrow \mathbf{N}$。</font>

`证明`：固定$m$，对$n$使用数学归纳法。首先$n=0$时，$0+m=m$是自然数；然后假设$n+m$是自然数，那么$(n++)+m=(n+m)++$是$n+m$的后继，当然也是自然数。$\square$

<font color=blue>**引理**：对任意自然数$m$，$m+0=m$。</font>

`证明`：数学归纳法。首先当$m=0$时，根据加法的定义，$0+0=0$；然后假设对自然数$m$，满足$m+0=m$，需证明$(m++)+0=m++$。根据加法的定义，$(m++)+0=(m+0)++$，根据归纳假设$m+0=m$，所以$(m++)+0=m++$。$\square$

<font color=blue>**引理**：对任意自然数$n$和$m$，满足$n+(m++)=(n+m)++$。</font>

`证明`：固定$m$，对$n$使用数学归纳法。首先当$n=0$时，根据引理，左边$0+(m++)=m++$且右边$(0+m)++=m++$，所以$0+(m++)=(0+m)++$；然后假设对自然数$n$，满足$n+(m++)=(n+m)++$，需证明$(n++)+(m++)=((n++)+m)++$。左边根据加法的定义$(n++)+(m++)=(n+(m++))++$，根据归纳假设$(n+(m++))++=((n+m)++)++$，而右边根据加法的定义$((n++)+m)++=((n+m)++)++$，所以$(n++)+(m++)=((n++)+m)++$。$\square$

<font color=green>**推论**：对任意自然数$n$，$n++=n+1$</font>

`证明`：根据定义$1=0++$，所以$n+1=(n+0)++=n++$。$\square$

所以自然数的后继（增量运算），可以通过加法来表示。

<font color=blue>**加法交换律**：$a+b=b+a$</font>

`证明`：固定$b$，对$a$使用数学归纳法。首先当$a=0$时，根据加法的定义和引理，$0+b=b$且$b+0=b$，所以$0+b=b+0$；然后假设对自然数$a$，满足$a+b=b+a$，需证明$(a++)+b = b+(a++)$。根据加法的定义$(a++)+b = (a+b)++$，根据引理$b+(a++)=(b+a)++$，根据归纳假设$a+b=b+a$，所以$(a+b)++=(b+a)++$，即$(a++)+b = b+(a++)$。$\square$

<font color=blue>**加法结合律**：$(a+b)+c=a+(b+c)$</font>

`证明`：固定$a$和$c$，对$b$使用数学归纳法。首先当$b=0$时，根据引理$(a+0)+c=a+c$，根据加法的定义$a+(0+c)=a+c$，所以$(a+0)+c=a+(0+c)$；然后假设对自然数$b$，满足$(a+b)+c=a+(b+c)$，需证明$(a+(b++))+c=a+((b++)+c)$。左边$(a+(b++))+c=((a+b)++)+c=((a+b)+c)++$，右边$a+((b++)+c)=a+((b+c)++)=(a+(b+c))++$，根据归纳假设$(a+b)+c=a+(b+c)$，所以$((a+b)+c)++=(a+(b+c))++$，即$(a+(b++))+c=a+((b++)+c)$。$\square$

根据加法结合律，在连续的加法中，可以省略括号而不会产生歧义。

<font color=blue>**加法消去律**：对自然数$a,b,c$，如果$a+b=a+c$，那么$b=c$。</font>

`证明`：固定$b$和$c$，对$a$使用数学归纳法。首先当$b=0$时，$0+b=0+c$，根据加法的定义有$b=c$；然后假设$a+b=a+c \implies b=c$，需证明$(a++)+b=(a++)+c \implies b=c$。根据加法的定义$(a++)+b=(a+b)++$且$(a++)+c=(a+c)++$，即$(a+b)++=(a+c)++$，根据Peano公理第四条，有$a+b=a+c$，根据归纳假设，即$b=c$。$\square$

### 3）乘法

**定义（乘法）**：定义二元运算“$\times$”，满足对任意自然数$m$，有$0 \times m=0$；并且对任意自然数$n$和$m$，满足$(n++)\times m=n \times m+m$。

与加法类似，乘法也是递归定义的。

<font color=green>**推论**：乘法运算是封闭的，即$\times: \mathbf{N} \times \mathbf{N} \rightarrow \mathbf{N}$。</font>

`证明`：固定$m$，对$n$使用数学归纳法。首先$n=0$时，$0\times m=0$是自然数；然后假设$n\times m$是自然数，那么$(n++)\times m=(n\times m)+m$是两个自然数相加，根据加法的定义，也是自然数。$\square$

<font color=blue>**引理**：对任意自然数$m$，$m \times 0=0$。</font>

`证明`：数学归纳法。首先当$m=0$时，根据乘法的定义，$0 \times 0=0$；然后假设对自然数$m$，满足$m \times 0=0$，需证明$(m++) \times 0=0$。根据乘法的定义，$(m++)\times 0=m \times 0+0$，根据归纳假设$m \times 0=0$，所以$(m++)\times 0=0+0=0$。$\square$

<font color=blue>**引理**：对任意自然数$n$和$m$，满足$n \times (m++)=n \times m+n$。</font>

`证明`：固定$m$，对$n$使用数学归纳法。首先当$n=0$时，根据引理，左边$0 \times (m++)=0$且右边$(0 \times m)+0=0+0=0$，所以$0 \times (m++)=0 \times m+0$；然后假设对自然数$n$，满足$n \times (m++)=n \times m+n$，需证明$(n++) \times (m++)=(n++) \times m+(n++)$。左边根据乘法、加法的定义和归纳假设$(n++) \times (m++)=n \times (m++)+(m++)=(n \times (m++)+m)++=(n \times m +n+m)++$，而右边根据乘法的定义和加法的引理$(n++) \times m+(n++)=(n \times m +m)+(n++)=(n \times m+m+n)++$，根据加法交换律$n+m=m+n$，所以$(n++) \times (m++)=(n++) \times m+(n++)$。$\square$

按照代数的惯例，在不造成歧义时，可以省略乘号$\times$，即将$n \times m$简写为$mn$。

<font color=blue>**乘法交换律**：$ab=ba$</font>

`证明`：固定$b$，对$a$使用数学归纳法。首先当$a=0$时，根据乘法的定义和引理，$0\times b=0$且$b\times 0=0$，所以$0\times b=b\times 0$；然后假设对自然数$a$，满足$ab=ba$，需证明$(a++)b = b(a++)$。根据乘法的定义$(a++)b = ab+b$，根据引理$b(a++)=ba+b$，根据归纳假设$ab=ba$，所以$(a++)b = b(a++)$。$\square$

<font color=green>**推论**：对任意自然数$n$，$n\times 1=1\times n=n$。</font>

`证明`：利用乘法交换律，只需证明$n\times 1=n$。根据定义和引理，$n\times 1=n\times (0++)=n\times 0 + n=0+n=n$。$\square$

<font color=blue>**乘法分配律**：$a(b+c)=ab+ac$</font>

`证明`：固定$a$和$c$，对$b$使用数学归纳法。首先当$b=0$时，$a(0+c)=ac$而$a \times 0+ac = 0+ac = ac$，所以$a(0+c)=a \times 0+ac$；然后假设对自然数$b$，满足$a(b+c)=ab+ac$，需证明$a((b++)+c)=a(b++)+ac$。左边根据加法的定义、乘法的引理和归纳假设$a((b++)+c)=a((b+c)++)=a(b+c)+a=ab+ac+a$，右边乘法的引理和加法交换律$a(b++)+ac=ab+a+ac=ab+ac+a$，所以$a((b++)+c)=a(b++)+ac$。$\square$

同样地，可以证明$(a+b)c=ac+bc$

<font color=blue>**乘法结合律**：$(ab)c=a(bc)$</font>

`证明`：固定$a$和$c$，对$b$使用数学归纳法。首先当$b=0$时，根据引理$(a \times 0)c=0 \times c=0$，根据乘法的定义$a(0\times c)=a \times 0=0$，所以$(a\times 0)c=a(0\times c)$；然后假设对自然数$b$，满足$(ab)c=a(bc)$，需证明$(a(b++))c=a((b++)c)$。左边根据乘法的引理和乘法分配律$(a(b++))c=(ab+a)c=(ab)c+ac$，右边$a((b++)c)=a(bc+c)=a(bc)+ac$，根据归纳假设$(ab)c=a(bc)$，所以$(ab)c+ac=a(bc)+ac$，即$(a(b++))c=a((b++)c)$。$\square$

<font color=blue>**乘法消去率**：对自然数$a,b,c$，如果$ac=bc$且$c \neq 0$，那么$a=b$。</font>

`证明`：因为$c \neq 0$，所以存在唯一的自然数$d$满足$d++=c$，则命题等价为如果$a(d++)=b(d++)$，那么$a=b$。固定$a$和$b$，对$d$使用数学归纳法。首先当$d=0$时，$a(0++)=a\times 0+a=a$且$b(0++)=b$，所以$a=b$；然后假设$a(d++)=b(d++)$，需证明$a((d++)++)=b((d++)++) \implies a=b$。根据乘法的定义和引理$a((d++)++)=a(d++)+a$且$b((d++)++)=b(d++)+b$，通过归纳假设$a(d++)=b(d++)$，根据加法消去律得到$a=b$。$\square$

**定义（指数运算）**：定义二元运算“$\exp$”，满足对任意自然数$m$，有$\exp(m,0)=1$，记作$m^0=1$；并且对任意自然数$n$和$m$，满足$\exp(m,n++)=\exp(m,n) \times m$，记作$m^{n++}=m^n \times m$。

### 4）序关系

**定义（正的）**：一个自然数是正的，当且仅当它不等于0，即正自然数，简称正数。

<font color=green>**推论**：对正数$a$和自然数$b$，$a+b$是正的</font>

`证明`：固定$a$，对$b$使用数学归纳法。首先当$b=0$时，$a+0=a$是正的；然后假设$a+b$是正的，需证明$a+(b++)$是正的。因为$a+(b++)=(a+b)++$，根据Peano公理第三条，$(a+b)++ \neq 0$，所以$a+(b++)$是正的。$\square$

**定义（序）**：对任意自然数$a$和$b$，如果存在一个自然数$c$，满足$a=b+c$，则称$a$大于等于$b$，记作$a \geq b$或$b \leq a$；当$a \geq b$且$a \neq b$时，称$a$（严格）大于$b$，记作$a > b$或$b < a$。

**自然数的序的性质**：设$a,b,c$是自然数，那么：

1. （序是自反的）$a \geq a$；
2. （序是传递的）如果$a \geq b$且$b \geq c$，那么$a \geq c$；
3. （序是反对称的）如果$a \geq b$且$b \geq a$，那么$a = b$；
4. （加法保序）$a \geq b$当且仅当$a+c \geq b+c$；
5. （乘法保序）如果$a < b$且$c$是正的，那么$ac < bc$
6. $a < b$当且仅当$a++ \leq b$；
7. $a < b$当且仅当存在正数$c$，$b=a+c$。

`证明`：

1. 因为$a=a+0$，所以$a \geq a$；
2. 如果$a \geq b$且$b \geq c$，则存在自然数$d$和$e$，满足$a=b+d$和$b=c+e$，代入并根据加法结合律有$a=(c+e)+d=c+(e+d)$且$(e+d)$是自然数，所以$a \geq c$；
3. 如果$a \geq b$且$b \geq a$，则存在自然数$d$和$e$，满足$a=b+d$和$b=a+e$，代入有$a=a+e+d$，根据加法消去律有$0=e+d$，根据正数与自然数相加是正的，所以$e$和$d$都不是正数，即$e=d=0$，所以$a=b$；
4. $a \geq b \iff \exists d \in \mathbf{N}: a = b+d \iff a+c = b+d+c = b+c+d \iff a+c \geq b+c$；
5. 对$a < b$，存在正自然数$d$，满足满足$a=b+d$，所以$ac=bc+dc$。只需要证明正数$d$和正数$c$的乘积$dc$仍是正的。因为存在$e++=c$，所以$dc=d(e++)=de+d$，其中$de$是自然数、$d$是一个正数，所以$dc$是正的；
6. $a < b$当前仅当存在正数$c$满足$b=a+c$，即存在自然数$d$满足$d++=c$，所以等价于$b=a+(d++)=(a++)+d$，即$a++ \leq b$；
7. $a < b \iff \exists c \in \mathbf{N}: b=a+c \wedge b \neq a$。如果$c=0$，那么$b=a$，矛盾，所以$b \neq a$时$c$是正数。反之，如果$b=a$，那么$b=b+0=a+c$，根据加法消去律，有$0=c$，所以$c$是正数时$b \neq a$。$\square$

**自然数的序的三歧性**：对自然数$a$和$b$，下列三个命题中有且仅有一个是真的：

1. $a < b$；
2. $a = b$；
3. $a > b$。

`证明`：首先证明三个命题中最多有一个成立。如果$a < b$，根据定义有$a \neq b$；如果$a > b$，根据定义有$a \neq b$；如果$a < b$且$a > b$，根据自然数的序的性质第3条有$a = b$，矛盾。然后，再证明至少有一个命题成立。

固定$b$，对整个命题对$a$使用数学归纳法。当$a=0$时，因为$b=0+b$，所以$0\leq b$对任意自然数$b$成立，因此有$0<b$或$0=b$，满足整体命题成立。假设整体命题对$a$成立，需要证明对$a++$也成立。从$a < b$，$a = b$和$a > b$出发：
1. 如果$a > b$，即存在正数$c$满足$a=b+c$，所以$a++=(b+c)++=b+(c++)$，由$c++$一定是正的，有$a++ > b$；
2. 如果$a = b$，由$a++=a+1=b+1$，可得$a++ > b$；
3. 如果$a < b$，由自然数的序的性质第6条，有$a++ \leq b$，所以$a++<b$或$a++=b$。
即整体命题的归纳假设成立。$\square$

## 五、整数

严格来说，对限定在自然数上的初等数论可以不需要构造出整数，即只使用自然数及其加法、乘法的定义对证明算术基本定理就足够了。但是，为了在证明上方便地使用减法，并且在Tao的《陶哲轩实分析》一书中，通过形式减法构造整数、通过形式除法构造有理数、最后通过形式极限构造实数等数系是全书的一大精髓，所以这里保留整数的内容，但再不对每个结论做出详细证明。

### 1）形式化整数

**定义（整数）**：一个整数是一个形如$a\ominus b$的表达式，其中$a$和$b$自然数。$\ominus$代表形式减法，使得两个整数是相等的$a\ominus b=c\ominus d$当且仅当$a+d=c+b$。用$\mathbf{Z}$代表全体整数的集合。

<font color=blue>**定理**：对有序对$<a,b>\in \mathbf{N} \times \mathbf{N}$，形式减法$\ominus$是一个等价关系，所以形式减法构成一个划分，即整数是由形式减法$\ominus$的全体等价类（$\mathbf{N} \times \mathbf{N}$的非空子集）构成的一个集合。</font>

`证明`：根据形式减法的定义，显然满足自反性。证明对称性，假设$a\ominus b=c\ominus d$，即$a+d=c+b$，根据相等的对称公理有$c+b=a+d$，所以$c\ominus d=a\ominus b$。证明传递性，假设$a\ominus b=c\ominus d$且$c\ominus d=e\ominus f$，即$a+d=c+b$且$c+f=e+d$，两边对应相加即$a+d+c+f=c+b+e+d$，根据加法交换律、结合律和消去律，有$a+f=b+e=e+b$，所以$a\ominus b=e\ominus f$。$\square$

**定义（整数的加法）**：两个整数的和$(a\ominus b)+(c\ominus d)$，定义为整数$(a + c)\ominus(b + d)$。

**定义（整数的乘法）**：两个整数的积$(a\ominus b)\times (c\ominus d)$，定义为整数$(ac + bd)\ominus(ad + bc)$。

<font color=green>**推论**：整数$a\ominus 0$与自然数$a$等价（同构）。</font>

### 2）整数的运算法则

**定义（整数的负运算）**：如果$a\ominus b$是一个整数，定义它的负数为整数$b\ominus a$，记作$-(a\ominus b)$。特别地，如果$n=n\ominus 0$是正自然数，那么它的负数$-n=0\ominus n$，称为负整数。

**整数的三歧性**：对整数$x$，下列三个命题中有且仅有一个是真的：

1. $x = 0$；
2. $x$等于一个正自然数$n$；
3. $x$是一个正自然数的负数$-n$。

**整数的运算法则**：设$x,y,z$是整数，那么：

1. $x+y=y+x$
2. $(x+y)+z=x+(y+z)$
3. $x+0=0+x=x$
4. $x+(-x)=(-x)+x=0$
5. $xy=yx$
6. $(xy)z=x(yz)$
7. $x1=1x=x$
8. $x(y+z)=xy+xz$
9. $(y+z)x=yx+zx$

在抽象代数中，上面9条恒等式判定了全体整数构成了一个交换环。

**定义（减法运算）**：两个整数$x$和$y$的减法运算定义为$x-y=x+(-y)$。

根据整数的加法和负运算，可知减法运算是良定义的。

<font color=green>**推论**：$a-b=a+(-b)=(a\ominus 0)+(0\ominus b)=a\ominus b$，所以整数的减法运算与前面定义的形式减法是等价的。</font>

<font color=blue>**引理**：对整数$a$和$b$，如果$ab=0$，那么$a=0 \vee b=0$。</font>

**定义（序）**：对任意整数$a$和$b$，如果存在一个自然数$c$，满足$a=b+c$，则称$a$大于等于$b$，记作$a \geq b$或$b \leq a$；当$a \geq b$且$a \neq b$时，称$a$（严格）大于$b$，记作$a > b$或$b < a$。

**整数的序的性质**：设$a,b,c$是整数，那么：

1. $a > b$当且仅当存在正自然数$c$，$a=b+c$，即当且仅当$a-b$是正自然数；
2. （加法保序）$a \geq b$当且仅当$a+c \geq b+c$；
3. （正数乘法保序）如果$a > b$且$c$是正的，那么$ac > bc$；
4. （负运算反序）如果$a > b$，那么$-a < -b$；
5. （序的传递性）如果$a > b$且$b > c$，那么$a > c$；
6. （序的三歧性）命题中$a > b$，$a = b$，$a < b$有且仅有一个成立。

## 六、初等数论

<font color=blue>**定理**：对自然数$n$和正自然数$q$，存在自然数$m$和$r$，使得$0 \leq r < q$且$n=mq+r$。</font>

`证明`：固定正数$q$，对自然数$n$使用数学归纳法。首先当$n=0$时，存在$m=r=0$且$0 \leq r < q$使得$0=0\times q + 0$。假设命题在$n$时成立，则对$n++$有$n++=(mq+r)++=mq+(r++)$，因为$r < q$，所以$r++ \leq q$，如果$r++ < q$，则命题成立；如果$r++ = q$，那么$n++=mq+q=(m+1)q$，命题也成立。$\square$

<font color=green>**推论**：对整数$n$和正自然数$q$，存在整数$m$和自然数$r$，使得$0 \leq r < q$且$n=mq+r$。</font>

`证明`：当整数$n \geq 0$时，即$n$是自然数，上面的定理已经证明了。当整数$n < 0$时，可知它的负数$-n$为自然数，满足上面的定理，则$-n=mq+r$，即$n=-(mq+r)=(-m)q+(-r)$。当$r=0$时，得证；当$r\neq 0$时，因为$0 < r < q$，可得$0 < (q-r) < q$，所以有$n=(-m-1)q+(q-r)$，得证。$\square$

### 1）整除

**定义（整除、因数）**：对整数$a$和$b$，如果存在整数$c$，使得$a=bc$，那么称$a$被$b$整除、$b$整除$a$、$b$是$a$的因数、$a$是$b$的倍数，记作$b\mid a$。当$b$不整除$a$时，记作$b\nmid a$。

<font color=blue>**引理（整除的性质）**：$b\mid a \iff (-b)\mid a \iff b\mid (-a) \iff (-b)\mid (-a)$</font>

`证明`：由$a=bc=(-b)(-c)$以及$-a=-(bc)=(-b)c=b(-c)$，即证。$\square$

<font color=blue>**引理**：如果$b\mid a$且$c\mid b$，那么$c\mid a$。</font>

`证明`：根据整除的定义，存在整数$d$和$e$，使得$a=bd$且$b=ce$，即$a=(ce)d=c(ed)$，所以$c\mid a$。$\square$

**定义（绝对值）**：整数$a$的绝对值$|a|$，定义为当$a > 0$时，$|a|=a$；当$a = 0$时，$|a|=0$；当$a < 0$时，$|a|=-a$。

<font color=green>**推论**：如果$|a| < |b| \wedge |b| \mid |a|$，那么$a=0$。</font>

`证明`：根据绝对值的定义，易知整数的绝对值为自然数。由$|b| \mid |a|$，可知存在整数$c$，使得$|a|=|b|c$，易知$c\geq 0$。但是当$c > 0$即$c \geq 1$时，$|a| < |b| \leq |b|c$，与$|a|=|b|c$矛盾。因此只能有$c = 0$，此时$|a|=|b|\times 0 = 0$，根据绝对值的定义，即$a = 0$。$\square$

<font color=green>**推论**：如果对自然数$a$和$b$，$b\mid a \wedge a\neq 0$，那么$0 < b \leq a$。即一个正整数的因数小于等于其自身。</font>

`证明`：因为$b$是自然数，所以$b \geq 0$。如果$b = 0$，由整除的定义，有$a=0$，矛盾，所以$b > 0$。假设$b > a$，根据上一个推论，可得$a=0$，矛盾，所以$0 < b \leq a$。$\square$

<font color=green>**推论**：对整数$n$和正自然数$q$，使得$0 \leq r < q$且$n=mq+r$的整数$m$和自然数$r$是唯一的。</font>

`证明`：假设还存在一组整数$m'$和自然数$r'$也满足$n=m'q+r'$。则有$mq+r=m'q+r'$，即$(m-m')q=r'-r$，所以$q \mid (r'-r)$。根据引理和绝对值的定义有$q \mid |r'-r|$，根据$0 \leq r,r' < q$，易知$|r'-r| < q$，又因为$q$是正自然数有$q=|q|$，所以根据上面的推论有$r'-r = 0$，即$r'=r$。所以$(m-m')q=r'-r=0$，因为$q$是正自然数，所以只能是$m-m' = 0$，即$m=m'$。$\square$

### 2）质数

**定义（质数）**：如果$a$是一个大于$1$的整数（当然也是自然数），除了1和此数自身外，无法被其他自然数整除（其他自然数都不是$a$的因数），则称$a$为质数，又称素数。

**定义（合数）**：如果$a$是一个大于$1$的整数，且$a$不是质数，则称$a$为合数。因此，合数$a$存在除了$1$和$a$外的其他因数。

<font color=green>**推论**：对自然数$a$，下面三个命题有且仅有一个成立：（1）$a=1$；（2）$a$是质数；（3）$a$是合数。</font>

**定义（质因数）**：如果$a$的因数$b$是质数，那么称$b$是$a$的质因数。

<font color=blue>**引理**：如果$a$是一个大于$1$的整数，那么$a$的大于$1$的最小因数一定是质数。</font>

`证明`：因为$a$是一个大于$1$的整数，所以$a$要么是质数、要么是合数。如果$a$是质数，则$a$只有因数$1$和自身$a$，所以大于$1$的最小因数就是$a$，是质数；如果$a$是合数，假设大于$1$的最小因数$b$不是质数而是合数，那么根据合数的定义，$b$存在不等于$1$和其自身的因数$c$，又因为自然数的因数小于等于其自身，同时$b$的因数$c$也是$a$的因数，即$a$存在比$b$更小的大于$1$的因数，与假设矛盾，所以$b$是质数。$\square$

<font color=blue>**定理**：有无限多个质数。</font>

`证明`：（欧几里得证明）假设有限个质数的集合$\lbrace p_1, \dots, p_r \rbrace$，考虑$n=p_1 p_2 \dots p_r + 1$，其中$p_1 p_2 \dots p_r$是从$p_1$到$p_r$的连乘积（遍历集合）。取$n$的质因数$p$，可以证明$p$不属于集合$\lbrace p_1, \dots, p_r \rbrace$，否则$p$是$p_1 p_2 \dots p_r$的因数，那么也是$n-p_1 p_2 \dots p_r = 1$的因数，矛盾。从而任何有限集合$\lbrace p_1, \dots, p_r \rbrace$不可能包括所有的素数。$\square$

这个定理，也是《数学天书中的证明》中的第一个证明，该书中还给出了其他五种证明方法，有兴趣的可以看一看。

**定义（公因数、最大公因数）**：如果正整数$c$即是$a$的因数又是$b$的因数，那么称$c$是$a$和$b$的公因数，又称公约数。$a$和$b$的公因数中最大的数，称为最大公因数，记作$gcd(a,b)$，简记为$(a,b)$。

对多个整数的最大公因数记为$(a_1,a_2,\dots,a_n)$或$gcd(a_1,a_2,\dots,a_n)$。

<font color=blue>**引理（欧几里得算法、辗转相除法）**：如果$a=bq+r$，那么$(a,b)=(b,r)$。</font>

`证明`：设$a$和$b$的最大公因数为$c$，则存在整数$a'$和$b'$满足$a=ca'$和$b=cb'$，代入$a=bq+r$有$ca'=cb'q+r$，即$r=ca'-cb'q=c(a'-b'q)$，所以$c$是$r$的因数，也是$b$和$r$的公因数。假设$c$不是$b$和$r$的最大公因数，那么还存在正整数$c'>c$是$b$和$r$的最大公因数，由$a=bq+r$可知$c'$也是$a$的因数，同时也是$b$的因数，这与$c$是$a$和$b$的最大公因数矛盾。$\square$

**定义（互质）**：如果$(a,b)=1$，那么称整数$a$和$b$互质。

**定义（公倍数、最小公倍数）**：如果正整数$c$即是$a$的倍数又是$b$的倍数，那么称$c$是$a$和$b$的公倍数。$a$和$b$的公倍数中最小的数，称为最小公倍数，记作$lcm(a,b)$，简记为$\lbrace a,b\rbrace$。

<font color=green>**推论**：最小公倍数是公倍数的因数。</font>

`证明`：设$m = \lbrace a,b\rbrace$和公倍数$m' \geq m$，存在整数$q$和$r$满足$m'=qm+r$且$0\leq r < m$。假设$r\neq 0$，因为$a$即是$m$的因数又是$m'$的因数，所以$a$也是$r=m'-qm$的因数，同理$b$也是$r$的因数，即$r$也是$a$和$b$的公倍数且$r < m$，与$m$是最小公倍数矛盾。所以$r = 0$，$m'=qm$即$m$是任意公倍数$m'$的因数。$\square$

<font color=blue>**引理**：如果$a$和$b$的最大公因数是$d$、最小公倍数是$m$，那么有$ab=dm$。</font>

`证明`：因为$ab$是$a$和$b$的公倍数，所以最小公倍数$m \mid ab$，即存在整数$q$，使得$ab = qm$。下面证明$q$即最大公因数是$d$。首先对最小公倍数$m$，存在整数$a'$和$b'$，使得$m = aa'$且$m = bb'$，所以$ab = qm = qaa' = qbb'$，即$a = qb'$且$b = qa'$，所以$q$是$a$和$b$的公因数。然后对$a$和$b$的任意公因数$c$，有$a=cc'$及$b=cc''$，设整数$t=cc'c''=ac''=bc'$，所以$t$是$a$和$b$的公倍数。根据最小公倍数是公倍数的因数，即存在$t' \geq 1$使得$t=mt'$，由$ab = qm$得$abt' = qmt' = qt$且$abt' = cc'cc''t' = ctt'$，即$ct'=q$，所以对任意公因数$c$都有$c \leq q$，即$q$是最大公因数。$\square$

### 3）算术基本定理

<font color=blue>**定理（强归纳法）**：令$P(n)$表示对自然数的任意一个命题，如果$P(0)$为真且当$\forall n \leq m: P(n)$为真时有$P(m++)$也为真，那么对任意自然数$n$，都有$P(n)$为真。</font>

`证明`：当$\forall n \leq m: P(n)$为真时，显然有$P(m)$为真，根据数学归纳法有强归纳法成立。$\square$

<font color=blue>**定理（超限归纳法）**：令$P(n)$表示对自然数的任意一个命题，如果当$\forall n < m: P(n)$为真时有$P(m)$也为真，那么对任意自然数$n$，都有$P(n)$为真。</font>

`证明`：当$m=0$时，自然数$n<0$是一个恒假命题，所以$\forall n < 0: P(n) \implies P(0)$恒为真。因此超限归纳法与强归纳法在命题上是等价的。$\square$

<font color=blue>**引理**：每一个大于$1$的整数$a$都可以分解成质因数的连乘积。</font>

`证明`：超限归纳法，假设$\forall a < n$时，大于$1$的整数$a$都可以分解成质因数的连乘积，考虑$a=n$。如果$n$是质数$p$，则$n=p$就满足了分解成质因数的连乘积形式。如果$n$是合数，则$n$存在大于$1$的最小质因数$p_1$，即存在整数$b$使得$n= p_1 b$，显然有$b < n$，所以根据归纳假设，$b$可以分解成质因数的连乘积$b = p_2 \dots p_r$，而$p_1$是质数，所以$n=p_1 p_2 \dots p_r$也可以分解成质因数的连乘积。$\square$

<font color=blue>**引理**：对质数$p$，$p \nmid a \iff (p,a)=1$。</font>

`证明`：如果$p \nmid a$，即$p$不是$a$因数，因为质数$p$只有两个因数$1$和自身$p$，所以$a$和$p$只有一个公因数$1$，即$(p,a)=1$；如果$(p,a)=1$，那么运用反证法，可知质数$p$不是$a$的因数，所以$p \nmid a$。

也可以证明等价的逆否命题：$p \mid a \iff (p,a)\neq 1$。如果$p \mid a$，那么$p$是$a$的因数且显然$p$是$p$的因数，所以$p$是$a$和$p$的公因数，即$(p,a)\neq 1$；如果$(p,a)\neq 1$，设$(p,a) = q$，则$q$是$p$的因数，根据质数的定义，$p$的因数除了$1$就是$p$，所以$q=p$，即$p$也是$a$的因数，所以$p \mid a$。$\square$

<font color=blue>**引理**：如果$(a,b)=1$且$a \mid bc$，那么$a \mid c$。</font>

`证明`：因为$(a,b)=1$，根据$ab=(a,b)\lbrace a,b \rbrace$，可得最小公倍数$\lbrace a,b \rbrace = ab$。同时，根据$a \mid bc$且$b \mid bc$，所以$bc$是$a$和$b$的公倍数。由最小公倍数是公倍数的因数，即存在整数$d$，使得$bc=abd$，利用乘法消去律，即$c=ad$，所以$a \mid c$。$\square$

<font color=blue>**引理**：当$a \mid b_1 \dots b_n$且$a$与任意$b_1,\dots,b_{n-1}$互质，那么$a \mid b_n$。</font>

`证明`：因为$(a,b_1)=1$且$a \mid b_1(b_2\dots b_n)$，所以$a \mid b_2\dots b_n$。对$b_2,\dots,b_{n-1}$依次运用引理，因为$n$是有限的，最终可以得到$a \mid b_n$。

现在大家肯定觉得这样证明不够严谨，因为我们没有定义什么叫“依次”。所以可以把引理改写为：当$a \mid b_1 \dots b_n q$且$a$与任意$b_1,\dots,b_n$互质，那么$a \mid q$。对$n$使用数学归纳法，首先当$n=0$时，即$b_1 \dots b_n q = q$，直接得证$a \mid q$；假设当$n$时命题成立，考虑$n+1$，即$a \mid b_1 \dots b_n b_{n+1} q$，根据归纳假设，可知$a \mid b_{n+1} q$，因为$a$与$b_{n+1}$互质，根据上面的引理，可得$a \mid q$。$\square$

<font color=blue>**引理**：如果$(a,b)=1$且$c \mid a$，那么$(b,c)=1$。</font>

`证明`：反证法，假设$(b,c)=m \neq 1$。那么$m$是$c$的因数，因为$c \mid a$，所以$m$也是$a$的因数，即$m > 1$是$a$和$b$的公因数，这与$(a,b)=1$矛盾。$\square$

<font color=blue>**引理**：如果$(a,b)=1$，那么$(a,bc)=(a,c)$。</font>

`证明`：设$(a,bc)=d$，因为$(a,b)=1$且$d \mid a$，则$(d,b)=1$，同时$d \mid bc$，则$d \mid c$，所以$d$是$a$和$c$的公因数。设$(a,c)=t$，因为$t \mid c$，则$t \mid bc$，所以$t$是$a$和$bc$的公因数。根据最大公因数的定义，有$d \leq t$且$t \leq d$，所以$d=t$。$\square$

<font color=blue>**引理**：$a$与任意$b_1,\dots,b_n$互质，则$a$与$b_1, \dots, b_n$的连乘积互质。</font>

`证明`：因为$(a,b_1)=1$，所以$(a,b_1 b_2\dots b_n)=(a,b_2\dots b_n)$。对$b_2,\dots,b_{n-1}$依次运用引理，因为$n$是有限的，最终可以得到$(a,b_1 \dots b_n)=(a,b_n)=1$。

同样地，更严谨地可以对$n$使用超限归纳法证明$\forall n:(a,b_n)=1 \implies (a, b_1 b_2\dots b_n)=1$。假设当$n<m$时命题成立，考虑$n=m$，根据归纳假设$(a,b_1 \dots b_{m-1})=1$，根据上一条引理，可知$(a,b_1 \dots b_{m-1}b_m)=(a,b_m)=1$。$\square$

<font color=blue>**引理**：如果质数$p$整除$a_1, \dots, a_n$的连乘积，那么存在一个$a_r$能被$p$整除（$1 \leq r \leq n$）。</font>

`证明`：反证法，假设不存在$a_r$能被$p$整除，所以任意$a_r$与$p$互质。根据上面的引理，可知$p$与$a_1, \dots, a_n$的连乘积互质，这与质数$p$整除$a_1, \dots, a_n$的连乘积矛盾。$\square$

<font color=blue>**引理**：如果质数$p$整除质数$p,\dots,p_n$的连乘积，那么存在一个$p_r=p$（$1 \leq r \leq n$）。</font>

`证明`：根据上面的引可知存在一个$p_r$能被$p$整除，因为$p_r$和$p$都是质数，根据质数的定义，只能有$p_r=p$。$\square$

<font color=blue>**定理（算术基本定理）**：不计质因数的次序，则只有唯一的方式将大于$1$的整数分解为质因数的连乘积。</font>

`证明`：将整数分解为质因数的连乘积$a=p_1 \dots p_n$。假设存在另一种将$a$分解为不同质因数的连乘积，设为$a=q_1 \dots q_m$，即
$$p_1 \dots p_n=q_1 \dots q_m$$
所以$p_1 \mid q_1 \dots q_m$，根据上一条引理，存在一个$q_r=p_1$，因为不考虑分解的次序，不失一般性，可以设这个$q_r$就是$q_1$，则有$p_2 \dots p_n=q_2 \dots q_m$。这样依次运用引理，且$n$和$m$是有限的，最后肯定得到$p_n=q_m$且$n=m$。

更严谨地，可以使用集合论的语言。定义质数集为$\mathbf{P}$，因为可以把整数$n$分解为质因数的连乘积，这些质因数的集合设为$A = \lbrace p \in \mathbf{P} | p \mid a \rbrace = \lbrace p_1,\dots,p_r \rbrace$，注意到某个质因数可能在分解的连乘积中出现多次，同时考虑到这个集合是有限的，所以可以将质因数从小到大排列，即$p_1 < \dots < p_r$，运用指数运算可以将分解式表示为：$a=p_1 ^{\alpha_1} \dots p_r ^{\alpha_r}$，其中$\alpha_1,\dots,\alpha_r$是正自然数。

为了方便，先证明一个引理：质数$p$的$\alpha$次方（$\alpha \geq 1$）整除$a$，当且仅当分解的连乘积$a=p_1 ^{\alpha_1} \dots p_n ^{\alpha_n}$中，存在一个$p_r=p$且$\alpha \leq \alpha_r$（$1 \leq r \leq n$）。首先，当存在$p_r=p$且$\alpha \leq \alpha_r$时，显然有$p^{\alpha} \mid a$。然后，根据$p^{\alpha} \mid a$且$p \mid p^{\alpha}$（$\alpha \geq 1$），有$p \mid a$，所以根据上面的引理存在一个$p_r = p$。假设$\alpha > \alpha_r$，则根据整除的定义，存在一个整数$b$使得$a=bp^{\alpha}=bp_r ^{\alpha} = p_1 ^{\alpha_1} \dots p_n ^{\alpha_n}$，利用乘法消去律，左右消去$p_r^{\alpha_r}$，有$bp_r ^{\alpha - \alpha_r} = p_1 ^{\alpha_1} \dots p_{r-1} ^{\alpha_{r-1}} p_{r+1} ^{\alpha_{r+1}} \dots p_n ^{\alpha_n}$且$\alpha - \alpha_r \geq 1$，即$p_r ^{\alpha - \alpha_r} \mid p_1 ^{\alpha_1} \dots p_{r-1} ^{\alpha_{r-1}} p_{r+1} ^{\alpha_{r+1}} \dots p_n ^{\alpha_n}$，所以仍然存在一个$p_i=p$（$1 \leq i \leq n$且$i \neq r$），这与分解的连乘积中的质因数两两不等矛盾，所以$\alpha \leq \alpha_r$。

构造集合$B = \lbrace <p, \alpha> \in \mathbf{P}\times \mathbf{N} | \forall p \in \mathbf{P},\forall \alpha \in \mathbf{N} \wedge \alpha \geq 1: p^{\alpha} \mid a \wedge p^{\alpha+1} \nmid a \rbrace$，即集合$B$的元素是由满足质因数$p$的$\alpha$次方整除$a$但$p$的$\alpha+1$次方不整除$a$的有序对$<p, \alpha>$组成的。

现在要证明质因数的连乘积分解式$a=p_1 ^{\alpha_1} \dots p_r ^{\alpha_r}$右边的指数项，与集合$B$中的元素是一一对应的。

首先对任意一个质因数$p_i \in A$，根据连乘积分解式，显然满足$p_i ^{\alpha_i} \mid a$。如果假设$p_i ^{\alpha_i+1} \mid a$，利用引理可知存在矛盾，所以分解式$a=p_1 ^{\alpha_1} \dots p_r ^{\alpha_r}$右边的任意一个指数项$p_i ^{\alpha_i}$所对应的有序对$<p_i, \alpha_i>$都是集合$B$的元素。

然后，要证明集合$B$的任意元素$<p, \alpha>$所对应的所有指数项$p ^{\alpha}$的积等于$a$，即集合$B$中不存在多余的元素$<q, \beta>$其对应的指数项$q ^{\beta}$不在连乘积的分解式$a=p_1 ^{\alpha_1} \dots p_r ^{\alpha_r}$中。

我们假设集合$B$存在这样多余的元素$<q, \beta>$。根据集合$B$的定义$q^{\beta} \mid a$，根据引理即存在一个$p_i = q$且$\beta \leq \alpha_i$。又根据集合$B$的定义，有$q^{\beta+1} \nmid a$即$p_i^{\beta+1} \nmid a$，所以根据引理的逆否命题，有$\beta+1 > \alpha_i$，即$\beta \geq \alpha_i$，因此$\beta = \alpha_i$。

因此，质因数的连乘积分解式$a=p_1 ^{\alpha_1} \dots p_r ^{\alpha_r}$右边的指数项，与集合$B$中的元素是一一对应的。根据分类公理，可知集合$B$是唯一的，所以不计质因数的次序，将大于$1$的整数分解为质因数的连乘积的方式就是唯一。$\square$
