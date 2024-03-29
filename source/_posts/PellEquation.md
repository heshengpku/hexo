---
title: Pell方程的解法
date: 2023-05-25 19:23:02
category: Mathematics
tags:
 - Number Theory
mathjax: true
---

整理自John Stillwell的《Mathematics and Its History》（2010）的Chapter 5。

## Pell方程的描述

Pell方程（Pell's Equation）是一种特殊的丢番图方程（Diophantine Equation）。

即在整数域$\mathbb{Z}$中，求解如下形式方程的问题：

$$ x^2 - N y^2 = 1$$

其中$N$是一个不为平方数的正整数，使得$x$和$y$都为整数。

<!--more-->

## Pell方程的求解思路

首先，考虑一个比Pell方程更泛化的问题：

$$ x^2 - N y^2 = k$$

其中$k$为整数。这个问题的一组解可以用一个三元组表示为$(x,y,k)$。

利用恒等式：

$$(x_1^2 - N y_1^2)(x_2^2 - N y_2^2) = (x_1 x_2 + N y_1 y_2)^2 - N (x_1y_2 + x_2 y_1)^2$$

所以，如果$x_1$和$y_1$是$x^2 - N y^2 = k_1$的解，且$x_2$和$y_2$是$x^2 - N y^2 = k_2$的解，那么$x = x_1 x_2 + N y_1 y_2$和$y = x_1y_2 + x_2 y_1$是$x^2 - N y^2 = k_1 k_2$的解。

用三元组表示，即存在合成法则：由$(x_1,y_1,k_1)$和$(x_2,y_2,k_2)$，可以合成$(x_1 x_2 + N y_1 y_2,x_1y_2 + x_2 y_1,k_1 k_2)$。

为了求解Pell方程，可以先给定两个互素的数$a$和$b$（$\gcd(a,b)=1$），使得$a^2 - N b^2 = k$，即$(a,b,k)$。

将这个三元组与一个自然的解$(m,1,m^2-N)$（即$m^2 - N \times 1^2 = m^2 - N$）进行合成，得到$(am+Nb,a+bm,k(m^2-N))$。

从方程左右约去$k^2$，即得到一个（不一定是整数的）三元组：

$$(\frac{am+Nb}{k},\frac{a+bm}{k},\frac{m^2-N}{k})$$

现在，选取一个整数$m$使得$(a+bm)/k=b_1$为整数。可以证明，这使得$(am+Nb)/k=a_1$和$(m^2-N)/k=k_1$也都为整数。

如果选取的$m$使得$m^2-N$尽可能的小，那么最终三元组$(a_i,b_i,k_i)$将满足$k_i = \pm 1,\pm 2,\pm 4$。

这时，通过多次运用合成法则，就可以求解Pell方程。

## 示例

### 求解：$x^2 - 61y^2 = 1$

解：因为$8^2 - 61 \times 1^2 = 3$，即得到三元组$(8,1,3)$。

将$(8,1,3)$与$(m,1,m^2-61)$合成，得到三元组$(8m+61,8+m,3(m^2-61))$，即：

$$(\frac{8m+61}{3},\frac{8+m}{3},\frac{m^2-61}{3})$$

选择$m=7$（使得$m^2-61$尽可能的小，且$8+m$能被$3$整除），得到$(39,5,-4)$。

从方程左右约去$4$，得到$(39/2,5/2,-1)$。

将$(39/2,5/2,-1)$与其自身合成，得到$(1523/2,195/2,1)$，然后再次与$(39/2,5/2,-1)$合成，得到$(29718,3805,-1)$。

将$(29718,3805,-1)$与其自身合成，就得到$(1766319049, 226153980, 1)$。

即Pell方程$x^2 - 61y^2 = 1$的整数解为$x=1766319049, y=226153980$。

事实上，这也是Pell方程$x^2 - 61y^2 = 1$的最小整数解。

## 补充

### 1. 恒等式的证明

证明恒等式：

$$(x_1^2 - N y_1^2)(x_2^2 - N y_2^2) = (x_1 x_2 + N y_1 y_2)^2 - N (x_1y_2 + x_2 y_1)^2$$

等式左边：

$$(x_1^2 - N y_1^2)(x_2^2 - N y_2^2) = x_1^2 x_2^2 - N x_1^2 y_2^2 - N x_2^2 y_1^2 + N^2 y_1^2 y_2^2$$

等式右边：
$$\begin{aligned}
(x_1 x_2 + N y_1 y_2)^2 - N (x_1y_2 + x_2 y_1)^2 &= (x_1^2 x_2^2 + 2N x_1 x_2 y_1 y_2 + N^2 y_1^2 y_2^2) - N ( x_1^2 y_2^2 + 2 x_1 x_2 y_1 y_2 + x_2^2 y_1^2) \newline
&= x_1^2 x_2^2 - N x_1^2 y_2^2 - N x_2^2 y_1^2 + N^2 y_1^2 y_2^2
\end{aligned}$$

左右相等。$\square$

### 2. 整数解的证明

证明选取一个整数$m$使得$(a+bm)/k$为整数，则$(am+Nb)/k$和$(m^2-N)/k$也都为整数。

假设$a+bm = kl$（且$l$为整数），将一个$a=kl-bm$代入方程$a^2 - Nb^2=k$，得到:

$$\begin{align}
a (kl - bm) - N b^2 &= k \newline
akl - abm - N b^2 &= k \newline
(al - 1)k &= b(am + Nb)
\end{align}$$

因此$k$整除$b(am + Nb)$。

将$bm=kl-a$代入方程$a^2 m^2 - Nb^2 m^2=km^2$，得到：
$$\begin{align}
a^2 m^2 - N (kl-a)^2 &= km^2 \newline
a^2 m^2 - Nk^2 l^2 + 2Nkla - Na^2 &= km^2 \newline
(m^2 - N)a^2 &= (Nkl^2-2Nla+m^2)k
\end{align}$$

因此$k$整除$a^2(m^2 - N)$。

将$a+bm=kl$代入方程$(am + Nb)^2 - N(a+bm)^2 = k(m^2 - N)$，得到：
$$\begin{align}
(am + Nb)^2 - Nk^2 l^2 &= k(m^2 - N) \newline
(am + Nb)^2 &= k(m^2 - N + Nk l^2) \newline
a^2(am + Nb)^2 &= k((m^2 - N)a^2 + Nka^2 l^2) \newline
a^2(am + Nb)^2 &= k^2(Nkl^2-2Nla+m^2 + Na^2 l^2) \newline
\end{align}$$

因此$k^2$整除$a^2(am + Nb)^2$，即$k$整除$a(am + Nb)$。

由$k$整除$b(am + Nb)$和$k$整除$a(am + Nb)$可以得到$k$整除两个数的任意线性组合，即对于任意整数$r$和$s$有$k$整除$(ar + bs)(am + Nb)$，又因为$a$和$b$互素（$\gcd(a,b)=1$），所以根据Bezout引理，存在整数$r$和$s$使得$ar + bs = 1$。

因此$k$整除$am + Nb$。

因为$(a+bm)/k$为整数且$(am+Nb)/k$为整数，所以$(m^2-N)/k = [(a+bm)/k]^2 - N [(am+Nb)/k]^2$也为整数。$\square$
