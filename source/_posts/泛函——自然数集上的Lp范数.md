---
title: 泛函——自然数集上的Lp范数
date: 2017-10-08 22:12:27
categories: 数学
tags:
 - 泛函分析
description: "自然数集上的Lp范数与函数空间上的Lp范数."
---

## $L^p(\Omega,\mu)$ 空间

$1\leqslant p<\infty$, 设 $(\Omega,\mathscr{B},\mu)$ 是一个测度空间, $u$ 是 $\Omega$ 上的可测函数且 $|u(x)^p|$ 在 $\Omega$ 可积. 则记这样 $u$ 的全体为 $L^p(\Omega,\mu)$.

在 $L^p(\Omega,\mu)$ 中几乎处处相等的函数视为同一个函数后, $L^p(\Omega,\mu)$ 仍为线性空间. 定义
$$\Vert u\Vert=\left(\int\nolimits_\Omega \vert u(x)^p\vert {\rm d}\mu\right)^\frac{1}{p}$$
则 $\Vert\cdot\Vert$ 为一个范数. 事实上 $L^p(\Omega,\mu)$ 还是一个Banach空间.

## $\mathbb{N}$ 上的 $L^p$ 范数

以上的空间在 $\mathbb{N}$ 上的特殊情形如下:

$\Omega=\mathbb{N}, \mu( \&#123; n \&#125; )=1~(\forall n\in\mathbb{N})$, 此时 $L^p(\Omega,\mu)$ 由满足 $\sum\limits_{n=1}^\infty\vert u_n\vert^p<\infty$ 的所有序列组成, 记为 $l^p$.

此时其范数为 $$\Vert u\Vert=\left(\sum\limits_{n=1}^\infty \vert u_n\vert^p\right)^\frac{1}{p}.$$

对于 $\forall N\in\mathbb{N}$, 记 $f_N(x)=\sum\limits_{n=1}^Nu_n\chi_{ \&#123; n \&#125; }$, 令 $f(x)=\sum\limits_{n=1}^\infty u_n\chi_{ \&#123; n \&#125; }$, $x\in\mathbb{N}$.

则有
$$
\begin{align}
\int\nolimits_\mathbb{N}\vert f_N(x)\vert^p{\rm d}\mu(x) &= \int\nolimits_\mathbb{N}\sum\limits_{n=1}^Nu_n\chi_{ \&#123; n \&#125; }{\rm d}\mu(x) \\
&= \sum\limits_{n=1}^N \vert u_n\vert^p\cdot\int\nolimits_\mathbb{N}\chi_{ \&#123; n \&#125; }{\rm d}\mu(x) \\
&= \sum\limits_{n=1}^N \vert u_n\vert^p
\end{align}
$$

又显然, $0\leqslant\vert f_N(x)\vert^p\leqslant \vert f_{N+1}(x)\vert^p$, 且 $\vert f(x)\vert^p=\lim\limits_{N\to\infty}\vert f_N(x)\vert^p$.
故由非负可测函数的Levi定理有
$$
\begin{align}
\int\nolimits_\mathbb{N}\vert f(x)\vert^p{\rm d}\mu(x) &= \int\nolimits_\mathbb{N}\lim\limits_{N\to\infty}\vert f_N(x)\vert^p{\rm d}\mu(x) \\
&= \lim\limits_{N\to\infty}\int\nolimits_\mathbb{N}\vert f_N(x)\vert^p{\rm d}\mu(x) \\
&= \sum\limits_{n=1}^\infty \vert u_n\vert^p
\end{align}
$$

即有
$$ \left(\sum\limits_{N=1}^\infty\vert u_n\vert^p\right)^\frac{1}{p}=\left(\int\nolimits_\mathbb{N}\vert f(x)\vert^p{\rm d}\mu(x)\right)^\frac{1}{p}. $$