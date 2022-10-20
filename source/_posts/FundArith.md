---
title: 从大学数学到小学数学：从集合论证明算术基本定理
date: 2022-10-17 14:35:10
tags:
 - Math
mathjax: true
---

2022年，通读了《陶哲轩实分析》一书，其从Peano公理出发依次定义自然数、整数、有理数、实数和加减乘除、极限等运算，一气呵成、让人膜拜。

因为是关于数学分析的书，Tao只在第三章讲了集合论，而数理逻辑还在附录里。

但是我们常常听说“集合论是现代数学的基石”，那么理所当然是可以在定义数系之前，先做集合论的公理化。

因此我也想做一次尝试，在不做其他任何预设的情况下，完全从数学公理出发，进行一次“严谨的”推导。

当然有很多离散数学、集合论的课程就是这样做的。

不过个人能力有限，打算只证明一个小学生都知道的简单定理——算数基本定理，就收工。

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

这个集合可以记作 $A = \{ x | P(x) \}$。

<font color=blue>**罗素悖论**：不存在一个集合是由“所有不包括自身的集合”所组成的。</font>

`证明`：设命题$P(x)$为“所有不包括自身的集合$x$”，即$x \notin x$，根据概括公理，满足这个命题的对象$x$构成一个集合，设为集合$A = \{ x | x \notin x \}$。
然后讨论命题$A \in A$的真伪：
如果$A \in A$为真，那么$A$是自身的元素，根据集合$A$的定义，则满足命题$P(x)$，即$A \notin A$，矛盾；
如果$A \notin A$为真，那么满足命题$P(x)$，则根据集合$A$的定义，$A$是自身的元素，即$A \in A$，矛盾。$\square
所以数学需要更严格的公理化集合论，此处使用常见的ZFC集合论。

### 2）公理化集合论

#### 1. <font color=red>（外延公理）如果两个集合有相同的元素，那么它们相等。</font>

$\forall A, \forall B : (\forall x : x \in A \iff x \in B) \implies A=B$

#### 2. <font color=red>（空集公理）存在一个没有元素的集合。</font>

$\exists A, \forall x : x \notin A$

通过外延公理可以证明这个集合是唯一的，称这个集合为**空集**，记作 $\varnothing$。

#### 3. <font color=red>（配对公理）对任意两个集合$x$和$y$，存在一个集合有且只有$x$和$y$作为元素。</font>

$\forall x, \forall y, \exists A, \forall z : z \in A \iff z=x \vee z=y$

配对公理可以构造双元素集合，记作$\{x,y\}$。

当$x=y$时，也可以构造单元素集合：即对任意集合$x$，存在集合$\{x,x\}$，记作$\{x\}$。

#### 4. <font color=red>（并集公理）对任意集合$A$，存在一个集合$B$，$x$是$B$的元素当且仅当$x$是$A$的某个元素的元素。</font>

$\forall A, \exists B, \forall x : x \in B \iff (\exists y : x \in y \wedge y \in A)$

通过外延公理可以证明这个集合是唯一的，称这个集合为$A$的**并集**，记作 $\bigcup A$。

通过配对公理可以得到一个更容易理解的形式：对任意两个集合$A$和$B$，存在一个集合，其元素属于$A$或者属于$B$。

$\forall A, \forall B, \exists C, \forall x : x \in C \iff x \in A \vee y \in B$

`证明`：根据配对公理，对任意两个集合$A$和$B$，存在一个集合$\{A,B\}$。对集合$\{A,B\}$，根据并集公理，得到存在一个集合$C$，满足$x$是$C$的元素当且仅当$x$是$\{A,B\}$的元素的元素，即$x$是$A$的元素或$x$是$B$的元素。$\square$

通过外延公理可以证明这个集合是唯一的，称这个集合为$A$和$B$的**并集**，记作 $A \cup B$。

#### 5. <font color=red>（分类公理、子集公理）对任意集合$A$，存在一个集合$B$，$x$是$B$的元素当且仅当$x$是$A$的元素且命题$P(x)$成立。</font>

$\forall A, \exists B, \forall x: x \in B \iff x \in A \land P(x)$

这个集合$B$称作$A$的一个**子集**，记作 $B \subseteq A$，即 $B \subseteq A \iff (\forall x : x \in B \implies x \in A)$。

当$B \subseteq A$且$A \subseteq B$时，即$(\forall x : x \in B \implies x \in A) \wedge (\forall x : x \in A \implies x \in B)$，所以$\forall x : x \in B \iff x \in A$，根据外延公理，有$B=A$。

当$B \subseteq A$且$B \neq A$时，集合$B$称作$A$的一个**真子集**，记作 $B \subset A$。

通常定义子集的方式为$B=\{x \in A | P(x)\}$。

使用分类公理，对任意非空集合$A$，可以定义集合$A$的**交集**$\bigcap A$，即$\bigcap A$的元素都是$A$的任意元素的元素。

<font color=blue>**定理**：对任意非空集合$A$，它的交集存在。</font>

`证明`：因为$A$不是空集，所以存在一个元素$c \in A$，设命题$P(x)$为$\forall y \neq c (y\in A \implies x \in y)$，根据分类公理，存在一个集合$B$，满足$x \in B \iff x \in c \wedge \forall y \neq c(y \in A \implies x \in y) \iff \forall y (y\in A \implies  x \in y)$。$\square$

对任意两个集合$A$和$B$，存在一个集合，其元素属于$A$并且属于$B$，称这个集合为$A$和$B$的**交集**，记作 $A \cap B$。

#### 6. <font color=red>（幂集公理）对任意集合$A$，存在一个以$A$的全部子集为元素的集合。</font>

$\forall A, \exists B, \forall x: x \in B \iff x \subseteq A$

通过外延公理可以证明这个集合是唯一的，称这个集合是$A$的**幂集**，记作 $\mathcal{P}(A)$。

#### 7. <font color=red>（无穷公理）存在一个集合$\mathbf{N}$，使得空集是$\mathbf{N}$的元素，并且如果$x$是$\mathbf{N}$的元素，那么$x$与它的单元素集合$\{x\}$的并集也是$\mathbf{N}$的元素。</font>

$\exists \mathbf{N}: \varnothing \in \mathbf{N} \land (\forall x: x \in \mathbf{N} \implies x \cup \{x\} \in \mathbf{N})$

这种集合也称作**归纳集合**，其中可以定义集合$x$的**后继**为$x \cup \{x\}$。

这个公理的本质是：存在一个集合包含所有的自然数。

#### 8.  <font color=red>（替代公理）对任意集合$A$，存在一个集合$B$，$y$是$B$的元素当且仅当存在$x$是$A$的元素且命题$P(x,y)$为真。</font>

其中$P$是一个双变量谓词，对任何集合$x$存在唯一的集合$y$使$P(x,y)$成立，定义$F(x)=y$当且仅当$P(x,y)$。

$\forall A, \exists B, \forall y: y \in B \iff \exists x \in A : y = F(x)$

分类公理可以从替代公理和空集公理共同得出。

#### 9. <font color=red>（正则公理）对任意非空集合$A$，至少存在一个元素$x$，$x$与$A$的交集为空集</font>

$\forall A: A \neq \varnothing \implies \exists x \in A : x \cap A = \varnothing$

<font color=green>推论：不存在以自身为元素的集合。</font>

`证明`：反证法，假设存在一个集合$A$，使得$A$是自身的一个元素，即$A \in A$。根据配对公理，存在集合$B = \{A\}$。由于$B$中只有一个元素$A$，根据正则公理，有$A \cap \{A\} = \varnothing$。但根据假设有$A \in A$且$A \in \{A\}$，所以$A \cap \{A\} = \{A\} \neq \varnothing$，矛盾。$\square$

#### 10. <font color=red>（选择公理）给定一个集合$x$，其元素皆为互不相交的非空集合，那么存在一个集合$y$，包含$x$每一个元素的仅仅一个元素。</font>

$\forall X \left[ \varnothing \notin X \implies \exists f : X \rightarrow \bigcup X \quad \forall A \in X \, ( f(A) \in A ) \right]$

## 三、自然数与整数

### 1）Peano公理

对自然数最“自然”的定义，就是使用**Peano公理**：

#### 1. <font color=red>$0$是一个自然数。</font>

#### 2. <font color=red>如果$n$是一个自然数，那么$n$的后继也是一个自然数。</font>将$n$的后继记作$n++$。

#### 3. <font color=red>$0$不是任何自然数的后继。</font>等价于，对任意一个自然数$n$，有$n++$不等于$0$。

#### 4. <font color=red>对于不同的自然数，它们的后继也不同。</font>等价于，如果$n++=m++$，那么$n=m$。

#### 5. <font color=red>（数学归纳法）令$P(n)$表示对自然数的任意一个命题，如果$P(0)$为真且当$P(n)$为真时有$P(n++)$也为真，那么对任意自然数$n$，都有$P(n)$为真</font>

一般把全体自然数的集合记作$\mathbf{N}$。

通过集合论中的无穷公理和分类公理可以得到$\mathbf{N}$的存在性，通过外延公理可以证明$\mathbf{N}$的唯一性。

当然，更“纯粹”地，我们还可以直接从集合论公理中构造出自然数，试一试：

1. 根据空集公理，存在空集$\varnothing$，定义$0=\varnothing$；（对应Peano公理的第一条）
2. 定义集合$n$的后继为$n \cup \{n\}$，即定义$1=0++=0 \cup \{0\} = \varnothing \cup \{\varnothing\} = \{\varnothing\}$，依次类推可以定义$2=\{\varnothing, \{\varnothing\}\}$、$3=\{\varnothing, \{\varnothing\}, \{\varnothing, \{\varnothing\}\}\}$等等；（根据自然数的定义，对应可以证明Peano公理的第二、三、四、五条）

根据无穷公理，包括$\varnothing$及其全部后继的集合是存在的，可以把这一类集合称为归纳集合（inductive set）。

根据后继的定义，由$n \in n \cup \{n\}$且$n \subseteq n \cup \{n\}$，可以得到对任意自然数$n \in n++$且$n \subseteq n++$。

<font color=green>推论：$0$不是任何自然数的后继。</font>

`证明`:反证法，假设$0=n++$，即$\varnothing = n \cup \{n\}$，因为存在元素$n \in \{n\}$，根据并集公理$n \in n \cup \{n\}$，即$n \in \varnothing$，但是这与空集公理矛盾。$\square$

**自然数定义**：把包括$\varnothing$及其全部后继的“最小”集合称为自然数集。即对任意的归纳集合，自然数都是它们的元素，而自然数集就是全体自然数的集合。

<font color=blue>**定理1**：自然数集的存在性。即存在一个集合，它的元素是任意归纳集合的元素。</font>

`证明`：设$A$是一个归纳集合，根据无穷公理，这个集合是存在的。根据分类公理，设命题$P(x)$为“$x$是除$A$以外任意归纳集合的元素”，则存在一个集合$B$，满足
$x \in B \iff x \in A \wedge P(x) \iff x \text{是任意归纳集合的元素}$。$\square$

注意，这里不能直接从全体归纳集合的交集得到自然数集，因为“全体归纳集合”不是一个明确定义的集合。

<font color=blue>**定理2**：自然数集$\mathbf{N}$是归纳集合，并且$\mathbf{N}$是任意归纳集合的子集。</font>

`证明`：首先，根据归纳集合的定义，空集$\varnothing$是任意归纳集合的元素，所以$\varnothing \in \mathbf{N}$。然后，对于某个自然数$n \in \mathbf{N}$，自然数$n$是任意归纳集合的元素，则由归纳集合的定义，$n++$也是任意归纳集合的元素，则$n++ \in \mathbf{N}$，所以自然数集$\mathbf{N}$也是归纳集合。根据子集的定义，自然数集$\mathbf{N}$是任意归纳集合的子集。$\square$

<font color=blue>**定理3（数学归纳法）**：自然数集$\mathbf{N}$的任意子集，如果它是归纳集合，那么它等于自然数集。</font>

`证明`：设自然数集的子集$T=\{n \in \mathbf{N} | P(x) \}$，即$T \subseteq \mathbf{N}$为归纳集合；根据定理2，自然数集是归纳集合的子集，即$\mathbf{N} \subseteq T$，所以$T = \mathbf{N}$。$\square$

<font color=blue>**定理4**：任意一个非$0$的自然数都是某个自然数的后继。</font>

`证明`：设自然数集的子集$T=\{n \in \mathbf{N} | n = 0 \vee (\exists p \in \mathbf{N}: n = p++) \}$。因此$0 \in T$，并且如果$m \in T$，根据子集的定义，则$m \in \mathbf{N}$及$m++ \in \mathbf{N}$，即对于$m++$来说存在$p = m \in \mathbf{N}$满足$m++ = p++$，那么$m++ \in T$。因此$T$是归纳集合，根据定理3（数学归纳法），可得$T = \mathbf{N}$。$\square$

为了证明Peano假设的第四条，需要先引入一个“传递集”（transitive set）的概念。

**传递集**：如果集合$A$的元素的任意元素也是$A$的一个元素，那么集合$A$就被称为传递集，即$\forall x, \forall a : x \in a \wedge a \in A \implies x \in A$。

等价表述有：集合$A$的并集是自身的子集$\bigcup A \subseteq A$，集合$A$的元素也是自身的子集$a \in A \implies a \subseteq  A$。

<font color=blue>**定理5**：对于传递集$a$，有$\bigcup (a++) = a$。</font>

`证明`：根据后继的定义、集合并集的运算规则以及传递集的性质$\bigcup A \subseteq A$，有$\bigcup (a++) = \bigcup (a\cup \{a\}) = \bigcup a \cup \bigcup\{a\} = \bigcup a \cup a = a$。$\square$

<font color=blue>**定理6**：任意自然数是一个传递集。</font>

`证明`：设命题$P(n)$为“自然数$n$是一个传递集”，根据分类公理，存在一个集合$T=\{n \in \mathbf{N} | P(n)\}$。下面，通过定理3（数学归纳法）来证明$T=\mathbf{N}$。首先显然有$\varnothing \in T$，然后假设$k \in T$，根据定理5，有$\bigcup (k++) = k \subseteq k++$，所以$k++$是一个传递集，即$k++ \in T$，因此集合$T$是一个归纳集合，满足定理3。$\square$

<font color=green>推论：如果自然数$n++=m++$，那么$n=m$。</font>

`证明`：如果$n++=m++ \in \mathbf{N}$，则$\bigcup(n++)=\bigcup(m++)$。根据定理6，可知$n++$和$m++$都是传递集，根据定理5，则有$\bigcup(n++)=n$和$\bigcup(m++)=m$，所以$n=m$。

<font color=blue>**定理7**：自然数集$\mathbf{N}$是一个传递集。</font>

`证明`：即需要证明$\forall n \in \mathbf{N} : n \subseteq  \mathbf{N}$。根据分类公理，存在一个集合$T=\{n \in \mathbf{N} | n \subseteq  \mathbf{N}\}$。显然$\varnothing \in T$，然后假设$k \in T$，则有$k \subseteq \mathbf{N}$，同时$\{k\} \subseteq \mathbf{N}$，因此$k \cup \{k\} \subseteq \mathbf{N}$，即$k++ \in T$。所以集合$T$是一个归纳集合，根据定理3（数学归纳法）可知$T=\mathbf{N}$。$\square$

### 2）运算

#### 加法的定义

#### 乘法的定义

#### 自然数的运算法则

### 3）整数

#### 形式减法的定义
#### 整数的运算法则

## 四、初等数论

### 1）整除

### 2）质数

### 3）算数基本定理