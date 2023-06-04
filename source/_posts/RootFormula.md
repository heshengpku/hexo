---
title: Roots Formula of Quadratics, Cubics and Quartics
date: 2023-03-27 17:31:41
category: Mathematics
tags:
 - Algebra
 - Roots Formula
mathjax: true
---

## 一元二次、三次、四次方程的求根公式

整理自Joseph J. Rotman的《Advanced Modern Algebra》（Part 1，2015）的Chapter A-1。

<!--more-->

### 一元二次方程求根公式

对于一元二次方程：

$$ ax^2+bx+c = 0 $$

通过配方可以得到：

$$ (x+b/2a)^2 = -c/a+b^2/4a^2 $$

令判别式为：

$$ \Delta = b^2-4ac $$

可以得到求根公式为：

$$ x_1 = \frac{-b+\sqrt{\Delta}}{2a} $$
$$ x_2 = \frac{-b-\sqrt{\Delta}}{2a} $$

### 一元三次方程求根公式

对于一元三次方程：

$$ ax^3+bx^2+cx+d = 0 $$

做变量代换$X = x-\frac{b}{3a}$得到不包括二次项的形式：

$$ x^3+qx+r= 0 $$

可以得到求根公式为：

$$ x_1 = g+h $$
$$ x_2 = \omega g + \omega ^2 h$$
$$ x_3 = \omega ^2 g + \omega h $$

其中：

$$ g^3 = \frac{-r+\sqrt{R}}{2} $$
$$ h = -\frac{q}{3g} $$

其中：

$$ R = r^2+\frac{4}{27}q^3 $$
$$ \omega = -\frac{1}{2}+i\frac{\sqrt{3}}{2} $$

### 一元四次方程求根公式

对于一元四次方程：

$$ ax^4+bx^3+cx^2+dx+e = 0 $$

做变量代换$X = x-\frac{b}{4a}$得到不包括三次项的形式：

$$ x^4+qx^2+rx+s = 0 $$

左边可以化为一元二次方程的形式：

$$ x^4+qx^2+rx+s = (x^2+jx+l)(x^2-jx+m) $$

可以求得：

$$ l+m-j^2 = q $$
$$ j(m-l) = r $$
$$ lm = s $$

前两个方程可以得到：

$$ 2m = j^2 + q + r/j $$
$$ 2l = j^2 + q - r/j $$

代入第三个方程，可以得到$j^2$的三次方程：

$$ (j^2)^3 + 2q(j^2)^2 + (q^2-4s)j^2 - r^2 = 0 $$

通过一元三次方程的求根公式，可以得到$j^2$，然后得到$m$和$l$，再利用一元二次方程的求根公式得到最终的结果。